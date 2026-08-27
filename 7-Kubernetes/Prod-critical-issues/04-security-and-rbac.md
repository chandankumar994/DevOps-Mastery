# Security, RBAC, Secrets & Certificate Issues

This file covers access control misconfigurations, secret propagation problems, certificate expiry incidents, and private registry authentication failures.

---

## Issue 11: RBAC Misconfiguration Silently Breaking CI/CD Pipeline

- **Category**: Security & RBAC
- **Severity**: 🟠 High

### Scenario
A platform team tightened RBAC permissions across the cluster as part of a least-privilege security audit, revoking broad `cluster-admin` bindings that had been used (against best practice) by CI/CD service accounts for years. Shortly after, a critical hotfix deployment pipeline began failing.

### Symptoms
- CI/CD pipeline logs showed:
  ```
  Error from server (Forbidden): deployments.apps "checkout-service" is forbidden: 
  User "system:serviceaccount:ci-cd:deployer" cannot patch resource "deployments" 
  in API group "apps" in the namespace "prod-checkout"
  ```
- The failure only affected certain namespaces, not all — making it initially look like an intermittent/flaky pipeline issue rather than a permissions problem.

### Impact
- A critical security hotfix was delayed by 3 hours during an active incident window, extending exposure to a known vulnerability while the team debugged the "unrelated" pipeline failure.

### Root Cause Analysis
1. Reproduced the error manually with the service account's token to confirm it was a genuine permissions issue, not a transient API server error:
   ```bash
   kubectl auth can-i patch deployments --namespace prod-checkout --as=system:serviceaccount:ci-cd:deployer
   # no
   ```
2. Reviewed the new `RoleBinding`/`ClusterRoleBinding` objects introduced by the audit:
   ```bash
   kubectl get rolebinding,clusterrolebinding -A | grep deployer
   ```
   Found that the new least-privilege `Role` only granted access to a subset of namespaces (`staging`, `dev`) — `prod-checkout` had been omitted from the allow-list, likely due to a copy-paste error in the namespace list during the audit.
3. Root cause: an **incomplete namespace allow-list** in the new RBAC `RoleBinding` accidentally excluded a production namespace, silently breaking deployments to it while leaving others unaffected — masking the issue as "intermittent."

### Solution
1. Immediate fix: added the missing `RoleBinding` for the `prod-checkout` namespace:
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     name: deployer-binding
     namespace: prod-checkout
   subjects:
     - kind: ServiceAccount
       name: deployer
       namespace: ci-cd
   roleRef:
     kind: Role
     name: deployer-role
     apiGroup: rbac.authorization.k8s.io
   ```
2. Verified access was restored:
   ```bash
   kubectl auth can-i patch deployments --namespace prod-checkout --as=system:serviceaccount:ci-cd:deployer
   # yes
   ```
3. Audited all other namespaces against the CI/CD service account's expected permission scope to catch any other silent gaps from the same audit.

### Prevention / Best Practice
- Generate RBAC allow-lists from an authoritative source (e.g., a list of active namespaces queried dynamically) rather than manually maintained lists prone to typos/omissions.
- Add a CI/CD pipeline pre-flight check that runs `kubectl auth can-i` against all target namespaces before rollout, failing fast with a clear permissions error rather than during the actual deploy step.
- Treat RBAC changes as high-risk changes requiring staged rollout and validation against a full list of consuming pipelines/service accounts, not just spot-checks.
- Use tools like `rbac-lookup` or `kubectl-who-can` to audit effective permissions after any RBAC change.

---

## Issue 12: Rotated Secret Not Propagated to Running Pods

- **Category**: Security & RBAC
- **Severity**: 🟠 High

### Scenario
A security team rotated a database credential stored in a Kubernetes `Secret` as part of a quarterly rotation policy, updating it via a GitOps pipeline (ArgoCD). The `Secret` object updated successfully, but the application continued to fail authentication using the old password.

### Symptoms
- Application logs showed:
  ```
  FATAL: password authentication failed for user "app_user"
  ```
  starting immediately after the rotation, despite the new password being confirmed correct in the vault/secret store.
- `kubectl get secret db-creds -o yaml` showed the updated (new) value was correctly stored in the cluster.

### Impact
- 15-minute authentication outage for the affected service before the team realized pods weren't picking up the new secret automatically.

### Root Cause Analysis
1. Confirmed the `Secret` object itself was correctly updated in etcd via `kubectl get secret -o yaml`.
2. Checked how the Secret was consumed by the pod spec:
   ```yaml
   envFrom:
     - secretRef:
         name: db-creds
   ```
3. Root cause: Secrets mounted as **environment variables** are only read **once, at container start** — Kubernetes does not "hot reload" env vars into a running container when the underlying Secret changes. (This differs from Secrets mounted as **volumes**, which are updated periodically by the kubelet, typically within ~1 minute, though the application still needs to re-read the file.) Since no pod restart was triggered as part of the rotation pipeline, the already-running pods kept using the old credentials cached in their process environment.

### Solution
1. Immediate fix: manually triggered a rolling restart to force pods to pick up the new environment variable values:
   ```bash
   kubectl rollout restart deployment/checkout-service -n prod
   ```
2. Long-term fix: integrated a **Reloader** controller (e.g., Stakater Reloader) that watches Secrets/ConfigMaps and automatically triggers a rolling restart of dependent Deployments when they change:
   ```yaml
   metadata:
     annotations:
       reloader.stakater.com/auto: "true"
   ```
3. For workloads that can tolerate it, migrated to mounting secrets as **volumes** instead of env vars, and updated application code to re-read credentials periodically or on a `SIGHUP`, reducing (though not eliminating) the need for full restarts.

### Prevention / Best Practice
- Never assume Secret/ConfigMap updates propagate automatically to running pods consuming them as environment variables — always pair rotation with an explicit or automated restart mechanism.
- Adopt a Reloader-style controller as standard practice for any Deployment consuming Secrets/ConfigMaps that are rotated on a schedule.
- Where possible, use a dedicated secrets manager (Vault, AWS Secrets Manager with the CSI driver) with short-lived credentials and native rotation support that integrates with pod lifecycle.
- Document and test the full secret rotation runbook end-to-end (including restart) in staging before relying on it in production.

---

## Issue 13: Unnoticed TLS Certificate Expiry Causing Full Outage

- **Category**: Security & RBAC
- **Severity**: 🔴 Critical

### Scenario
A production Ingress controller (NGINX Ingress on a self-managed cluster) used a `cert-manager`-issued TLS certificate for the main customer-facing domain. `cert-manager` had been silently failing to renew the certificate for weeks due to an unrelated DNS validation issue, and no one noticed until the old certificate expired.

### Symptoms
- All HTTPS traffic to the application began failing with browser errors: `NET::ERR_CERT_DATE_INVALID`.
- `kubectl get certificate -n prod` showed:
  ```
  NAME              READY   SECRET            AGE
  app-tls-cert      False   app-tls-secret    92d
  ```
- `kubectl describe certificate app-tls-cert -n prod` revealed repeated failed `CertificateRequest` events referencing a DNS-01 challenge failure.

### Impact
- Full customer-facing outage for approximately 35 minutes until an emergency manual certificate was issued — all traffic over HTTPS was blocked by browsers/clients.

### Root Cause Analysis
1. Confirmed the expired cert was indeed the active one served by the Ingress:
   ```bash
   echo | openssl s_client -connect app.example.com:443 2>/dev/null | openssl x509 -noout -dates
   ```
2. Checked `cert-manager` controller logs:
   ```bash
   kubectl logs -n cert-manager deploy/cert-manager
   ```
   Found repeated errors:
   ```
   error presenting DNS01 challenge: could not find hosted zone for domain "app.example.com"
   ```
3. Traced back to a DNS provider account change (a Route53 IAM policy update) made weeks earlier that inadvertently revoked `cert-manager`'s permission to create the `_acme-challenge` TXT record needed for domain validation — the renewal had been silently failing ever since, well before the actual expiry.
4. Root cause: **no alerting existed on `cert-manager` renewal failures or certificate expiry dates**, so a permission change made for an unrelated reason silently broke auto-renewal for weeks with zero visibility until the hard expiry caused an outage.

### Solution
1. Immediate mitigation: manually issued a certificate via a fallback method (temporary self-issued cert from a backup CA account with correct permissions) to restore service, then fixed the IAM policy:
   ```bash
   aws iam put-role-policy --role-name cert-manager-role --policy-name dns01-access --policy-document file://fixed-policy.json
   ```
2. Triggered a manual renewal once permissions were restored:
   ```bash
   kubectl delete certificaterequest -n prod --all
   kubectl annotate certificate app-tls-cert -n prod cert-manager.io/issue-temporary-certificate="true" --overwrite
   ```
3. Verified successful renewal and correct certificate was being served.

### Prevention / Best Practice
- Set up dedicated alerting on both **certificate expiry date** (e.g., `certmanager_certificate_expiration_timestamp_seconds` Prometheus metric, alert at 30/14/7/1 day thresholds) and **renewal failure events**, not just relying on "no news is good news."
- Treat IAM/DNS provider permission changes as requiring a checklist review of all dependent automation (cert-manager, external-dns, etc.) before rollout.
- Run periodic synthetic checks that validate certificate validity from the outside (e.g., an external uptime monitor checking cert expiry, independent of in-cluster state).
- Consider maintaining a documented emergency manual-issuance runbook as a fallback for when automated renewal is broken and time is critical.

---

## Issue 14: Private Registry ImagePullBackOff from Expired Token

- **Category**: Security & RBAC
- **Severity**: 🟡 Medium

### Scenario
An application hosted on a self-managed Kubernetes cluster pulled container images from a private Google Artifact Registry using a long-lived service account JSON key stored as an `imagePullSecret`. During a scheduled node replacement (as part of an OS patching cycle), new pods scheduled onto the replaced nodes failed to start.

### Symptoms
- `kubectl get pods` showed:
  ```
  my-app-6c5f7d9b8-tz8kq   0/1   ImagePullBackOff   0   5m
  ```
- `kubectl describe pod my-app-6c5f7d9b8-tz8kq` showed:
  ```
  Failed to pull image "asia-south1-docker.pkg.dev/proj/repo/app:v42": 
  unauthorized: authentication failed
  ```
- Existing pods already running on untouched nodes continued working fine (since the image was already pulled/cached locally), which delayed detection of the underlying problem.

### Impact
- New pod scheduling and any future rolling deployments were blocked cluster-wide, though existing running workloads were unaffected — moderate but escalating risk since any restart or scale-up would fail.

### Root Cause Analysis
1. Verified the `imagePullSecret` itself:
   ```bash
   kubectl get secret regcred -n prod -o jsonpath='{.data.\.dockerconfigjson}' | base64 -d
   ```
2. Manually tested the embedded service account key against the registry:
   ```bash
   docker login -u _json_key --password-stdin https://asia-south1-docker.pkg.dev < key.json
   ```
   This failed with an authentication error.
3. Checked the GCP IAM console and found the service account key had been automatically rotated/revoked by an organizational key-rotation policy (90-day max key age) — the Kubernetes `Secret` still contained the old, now-invalid key, since nothing had updated it when the key was rotated at the IAM layer.
4. Root cause: **a static, long-lived service account key stored as a Kubernetes Secret was rotated at the source (GCP IAM) without a corresponding update to the in-cluster Secret**, breaking image pulls once the old key was invalidated.

### Solution
1. Immediate fix: generated a new service account key and updated the Kubernetes Secret:
   ```bash
   kubectl create secret docker-registry regcred \
     --docker-server=asia-south1-docker.pkg.dev \
     --docker-username=_json_key \
     --docker-password="$(cat new-key.json)" \
     --namespace=prod \
     --dry-run=client -o yaml | kubectl apply -f -
   ```
2. Verified new pods could pull images successfully.
3. Long-term fix: migrated to **Workload Identity** (GKE) / IAM Roles for Service Accounts equivalent, eliminating static keys entirely in favor of short-lived, automatically-refreshed tokens tied to the Kubernetes service account.

### Prevention / Best Practice
- Avoid static, long-lived service account keys for image pulls wherever the cloud provider supports workload identity federation (GKE Workload Identity, EKS IRSA, AKS Workload Identity).
- If static keys must be used, track their expiry/rotation schedule explicitly and automate `imagePullSecret` updates in lockstep with IAM-level key rotation (e.g., via an ExternalSecrets operator syncing from a secrets manager).
- Alert on `ImagePullBackOff`/`ErrImagePull` events cluster-wide, correlated with recent IAM/credential rotation events, to catch this class of issue proactively.
- Periodically test image pull credentials in a non-disruptive way (e.g., a scheduled canary pod) rather than discovering failures only during real deployments or node replacements.

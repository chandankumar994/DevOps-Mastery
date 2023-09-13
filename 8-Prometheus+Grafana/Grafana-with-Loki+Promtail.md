# Grafana with Loki and Promtail

![image](https://github.com/chandankumar994/DevOps-Mastery/assets/15160387/d7a9bfa6-1fe4-4ad0-a003-de50f1687c1d)

Source : [Video](https://www.youtube.com/watch?v=QwGm5m4AxNA/)

### Install Grafana on Debian or Ubuntu
```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key

https://apt.grafana.com/gpg.key
```
### Stable release:
```
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
### Beta release:
```
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

```
# Update the list of available packages
sudo apt-get update

# Install the latest OSS release:
sudo apt-get install grafana
```

### Install Loki and Promtail using Docker
**Note:** Make sure docker is inatall and required permissions are given:
```
sudo apt-get install docker.io
```
### Download Loki Config
```
mkdir Monitoring
cd Monitoring

wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml
```
### Run Loki Docker container
```
docker run -d --name loki -v $(pwd):/mnt/config -p 3100:3100 grafana/loki:2.8.0 --config.file=/mnt/config/loki-config.yaml
```
Note: you can check container by running `docker ps` command.

### Download Promtail Config
```
mkdir Monitoring # created above
cd Monitoring 

wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml

```
### Run Promtail Docker container
```
docker run -d --name promtail -v $(pwd):/mnt/config -v /var/log:/var/log --link loki grafana/promtail:2.8.0 --config.file=/mnt/config/promtail-config.yaml
```

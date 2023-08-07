# Docker Network:

### Explain the different types of docker networks:
- **Bridge Network:** The default network type in Docker. Containers attached to the bridge network can communicate with each other using IP addresses.
  ```
  docker run --network=bridge my-container
  ```

- **Host Network:** Containers share the host machine's network stack, meaning they use the host's networking directly. This can improve performance but might lead to port conflicts.
  ```
  docker run --network=host my-container
  ```

- **Overlay Network:** Used for multi-host communication in Docker Swarm mode, allowing containers running on different nodes to communicate with each other.
  ```
  docker network create --driver=overlay my-overlay-network
  docker service create --network=overlay my-overlay-network my-service
  ```

- **Macvlan Network:** Assigns a unique MAC address to each container, making them appear as physical devices on the network. Useful when containers need to appear as separate physical machines.
  ```
  docker network create -d macvlan --subnet=<subnet> gateway=<gateway> -o parent=<network-interface> my-macvlan-network
  docker run --network=my-macvlan-network my-container  
  ```

- **None Network:** Containers attached to this network have no network connectivity. It is used when you don't want a container to have any network access.
  ```
  docker run --network=none my-container
  ```

- **Custom User-defined Networks:** You can create custom networks using the docker network create command. These networks can be bridge, overlay, or MACVLAN types and allow you to manage container communication.
  - **Custom-Bridge-Network Example:**
    ```
    docker network create my-bridge-network
    docker run --network=mybridge-network my-container
    ```


### Which Networks works by default?
Bridge Network

```
# to check which network is running below is the command:
docker network ls

# or
docker network list
```


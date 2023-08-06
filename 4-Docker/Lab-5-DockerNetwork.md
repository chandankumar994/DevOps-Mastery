# Docker Network:

### Explain the different types of docker networks:
- **Bridge Network:** The default network type in Docker. Containers attached to the bridge network can communicate with each other using IP addresses.

- **Host Network:** Containers share the host machine's network stack, meaning they use the host's networking directly. This can improve performance but might lead to port conflicts.

- **Overlay Network:** Used for multi-host communication in Docker Swarm mode, allowing containers running on different nodes to communicate with each other.

- **Macvlan Network:** Assigns a unique MAC address to each container, making them appear as physical devices on the network. Useful when containers need to appear as separate physical machines.

- **None Network:** Containers attached to this network have no network connectivity. It is used when you don't want a container to have any network access.

- **Custom User-defined Networks:** You can create custom networks using the docker network create command. These networks can be bridge, overlay, or MACVLAN types and allow you to manage container communication.


### Which Networks works by default?
Bridge Network

```
# to check which network is running below is the command:
docker network ls

# or
docker network list
```


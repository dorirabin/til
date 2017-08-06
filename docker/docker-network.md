# Docker network

Containers within the same network can use container name to link to other containers without specifying the actual ip of them.

## Create `bridge` network

```shell
docker network create --driver bridge <network name>
```

## Launch container with specific network

```shell
docker run --network=<network name> -itd --name=<container name> <image name>
```

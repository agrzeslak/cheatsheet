---
layout: default
title: Docker
tags:
    - breakout
    - containerisation
    - kubernetes
---
# Docker
### Are we in a Docker container?
- Check for `/.dockerenv` or `/.dockerinit` (pre-v1.11)

```shell
cat /proc/1/cgroup
```
- some control groups will belong to docker if inside container (same for LXC)

```shell
cat /proc/1/sched | head -n 1
```
- init = not a container
- bash, etc. = container

### Is the Docker socket mounted?
```shell
ls -al /var/run/docker.sock
```

```shell
find / -iname "docker.sock" 2>/dev/null
```
- if so, check if the Docker client is available or installable

### Useful Docker commands
```shell
docker version
```

List all running containers
```shell
docker container ls -a
```

List all containers
```shell
docker ps -a
```

Spawn shell inside container
```shell
docker exec -it [CONTAINER] /bin/sh
```

Get IP address
```shell
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER]
```

### CVE-2019-5736 runc Docker escape
- Docker versions < 18.09.2 (`docker version`)
- runc versions <= 1.0-rc6 (`docker-runc -version`)
- <https://github.com/Frichetten/CVE-2019-5736-PoC>
- <https://www.exploit-db.com/exploits/46369>


### Enumeration within Container
```shell
for port in {1..65535}; do timeout 1 bash -c "echo > /dev/tcp/[HOST]/$port" >& /dev/null && echo "$port open"; done
```

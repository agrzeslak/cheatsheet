---
layout: default
title: Docker
tags:
    - breakout
    - containerisation
    - kubernetes
---
{% raw %}
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
sudo docker container ls -a
```

List all containers
```shell
sudo docker ps -a
```

Spawn shell inside container
```shell
sudo docker exec -it [CONTAINER] /bin/sh
```

Get container settings for a single container
```shell
sudo docker inspect [CONTAINER]
```

Get IP address
```shell
sudo docker inspect -f'{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER]
```

Get container settings for all containers
```shell
sudo docker inspect $(sudo docker ps -aq)
```

Get all IP addresses and ports
```shell
sudo docker inspect -f '{{printf "%.12s" .Id}} {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}} {{ .NetworkSettings.Ports }}' $(sudo docker ps -aq)
```

One liner for interesting info
```shell
sudo docker inspect -f '{{printf "%.12s" .Id}} User:{{.Config.User}} Priv:{{.HostConfig.Privileged}} IPs:{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}} Ports:{{ .NetworkSettings.Ports }}' $(sudo docker ps -aq)
```

Execute command in all containers (e.g. all listening port)
```shell
for container in $(sudo docker ps -q); do echo $container; out=$(sudo docker exec $container netstat -ntlp); echo $out | grep -i listen; done
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
{% endraw %}
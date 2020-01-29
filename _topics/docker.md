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
```shell
ls -al /.dockerenv
ls -al /.dockerinit  # pre-v1.11
```

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
find / -iname "docker.sock" 2>/dev/null
```
- if so, check if the Docker client is available, installable or you can transfer the binary

### Useful Docker commands
```shell
docker version
sudo docker container ls -a                # List all running containers
sudo docker ps -a                          # List all containers
sudo docker exec -it [container] /bin/sh   # Spawn shell inside container
sudo docker inspect [container]            # Get container settings for a single container
sudo docker inspect $(sudo docker ps -aq)  # Get container settings for all containers
```

Get IP address
```shell
sudo docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container]
```

Get all IP addresses and ports
```shell
sudo docker inspect -f '{{printf "%.12s" .Id}} {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}} {{ .NetworkSettings.Ports }}' $(sudo docker ps -aq)
```

One liner for interesting info
```shell
sudo docker ps -q | xargs sudo docker inspect -f '[{{printf "%.12s" .Id}} {{.Name}}] NetworkMode={{.HostConfig.NetworkMode}} PidMode={{.HostConfig.PidMode}} Privileged={{.HostConfig.Privileged}} User={{.Config.User}} AppArmorProfile={{.AppArmorProfile}} SecurityOpt={{.HostConfig.SecurityOpt}} RORootFS={{.HostConfig.ReadonlyRootfs}} UserNSMode={{.HostConfig.UsernsMode}} CapAdd={{.HostConfig.CapAdd}} CapDrop={{.HostConfig.CapDrop}}'
```

User namespace support enabled? (Are root users within container mapped to less than root on the host?)
```shell
sudo docker ps -q | xargs -I% sh -c 'echo -e "[%]"; ps -p $(sudo docker inspect -f "{{.State.Pid}}" %) -o pid,user'
```

Execute command in all containers (e.g. all listening port)
```shell
sudo docker ps -q | xargs -I% sh -c 'echo %; sudo docker exec % netstat -ntlp | grep -i listen;'
```

### CVE-2019-5736 runc Docker escape
- Docker versions < 18.09.2 (`docker version`)
- runc versions <= 1.0-rc6 (`docker-runc -version`)
- <https://github.com/Frichetten/CVE-2019-5736-PoC>
- <https://www.exploit-db.com/exploits/46369>


### Enumeration within Container
```shell
for port in {1..65535}; do timeout 1 bash -c "echo > /dev/tcp/<host>/$port" >& /dev/null && echo "$port open"; done
```
{% endraw %}
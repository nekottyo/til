# docker container内の nameserver 固定

`/etc/docker/daemon.json` に記述

dockerd の option は https://docs.docker.com/engine/reference/commandline/dockerd/

```
{
    "fixed-cidr": "172.17.0.1/16",
    "dns": ["172.17.0.1"]
}
```

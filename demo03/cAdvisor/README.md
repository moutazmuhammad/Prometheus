# Demo 2 - Run C-Advisor

we need run [C-Advisor](https://github.com/google/cadvisor)as a container

### 1.1 Run C-Advisor Container

```

sudo docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  --privileged \
  --device=/dev/kmsg \
  gcr.io/cadvisor/cadvisor

```

* The [C-Advisor](https://github.com/google/cadvisor) lsitens on HTTP port `8080` by default.

* Go to `/etc/prometheus/prometheus.yml` to add another item in the scrabers section. 



# 03 — Docker services and monitoring

## Containers

| Container | Image | Published IPv4 port | Persistent storage | Restart |
|---|---|---|---|---|
| `uptime-kuma` | `louislam/uptime-kuma:1` | `0.0.0.0:3001:3001` | `uptime-kuma:/app/data` | `always` |
| `portainer` | `portainer/portainer-ce` | `0.0.0.0:9000:9000` | Existing named volume mounted at `/data` | `always` |

Portainer also mounts `/var/run/docker.sock`, which grants significant control of the Docker host. Keep Portainer administrator credentials strong and limit access. Container-internal ports `8000` and `9443` were not published to the host in the recorded configuration.

## Docker networking

Created user-defined bridge `monitoring` (`203.0.113.0/24`). Kuma and Portainer are both attached; Kuma monitors Portainer using:

```text
http://portainer:9000
```

Pi-hole is a service on the Debian host, not part of this Docker bridge:

```text
http://192.0.2.3/admin/
```

## Reboot/startup fix

Originally the containers published `192.0.2.3:3001` and `192.0.2.3:9000`. At boot, the address was not ready and Docker failed with `bind: cannot assign requested address`. The containers were recreated with **IPv4-only** bindings to `0.0.0.0`, while ingress filtering remained in `DOCKER-USER`.

Binding to all IPv4 host interfaces expands where the port listens; the deployment therefore depends on the active firewall and correct interface filtering. This repository documents configuration, not an independent external reachability audit.

## Operational checks

```sh
docker ps
docker network inspect monitoring
sudo ss -lntp | grep -E ':3001|:9000'
docker exec uptime-kuma curl --connect-timeout 5 -I http://portainer:9000
docker exec uptime-kuma curl --connect-timeout 5 -I http://192.0.2.3/admin/
```

Expected lab results: Portainer `200 OK`, Pi-hole `302 Found`. These tests succeeded after a Debian reboot.

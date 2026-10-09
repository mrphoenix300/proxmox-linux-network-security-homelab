# 02 — Debian server and Pi-hole

## Purpose

VM 100 (`debian-lab`) is a minimal Debian Linux server on `vmbr0` at `192.0.2.3/24`. It hosts Pi-hole and Docker-based services and is administered over SSH from the home LAN.

## Observed services

| Service | Listening port(s) |
|---|---|
| SSH | TCP 22 |
| Pi-hole DNS | TCP/UDP 53 |
| Pi-hole web UI | TCP 80 and 443 |
| Pi-hole NTP | UDP 123 |
| Uptime Kuma (published Docker port) | TCP 3001 |
| Portainer (published Docker port) | TCP 9000 |

Pi-hole was checked from the Kuma container with:

```sh
docker exec uptime-kuma curl --connect-timeout 5 -I http://192.0.2.3/admin/
```

Observed result: `HTTP/1.1 302 Found`, redirecting to `/admin/login`. This confirms the HTTP service was reachable from the container; it does not prove every DNS filtering feature was tested.

## Administration

```sh
ssh <debian-user>@192.0.2.3
sudo ufw status verbose
sudo ss -tulpn
```

Do not disable SSH password authentication until key-based logins are tested from a second session. SSH-key-only hardening was listed as optional in the roadmap and was not established as completed.

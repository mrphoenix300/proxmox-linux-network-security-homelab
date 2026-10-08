# 06 — Validation and troubleshooting

## pfSense lab tests

Test commands from Alpine:

```sh
ip -4 addr show eth0
ip route
ping -c 4 10.50.10.1
ping -c 4 1.1.1.1
ping -c 4 8.8.8.8
nslookup google.com
```

Initially `eth0` had no DHCP address; running `ip link set eth0 up` and `udhcpc -i eth0 -n -q` obtained `10.50.10.100` from pfSense at `10.50.10.1`. Gateway, Internet ping and DNS resolution were subsequently successful.

## Firewall exercise: targeted ICMP block

In pfSense **Firewall -> Rules -> LAN**, created a rule **above** the default IPv4 allow:

- Action `BLOCK`; protocol IPv4 ICMP echo request
- Source: `10.50.10.100`
- Destination: `1.1.1.1`

Observed: ping to `1.1.1.1` failed (100% loss), while ping to `8.8.8.8` and DNS resolution succeeded. The test rule was then deleted and changes applied; ping to `1.1.1.1` succeeded again.

## Docker incident: time-of-boot host address

**Symptom:** Docker containers did not start after Debian reboot (`Exited (128)`/`Exited (2)`).

**Evidence:** `docker inspect` error strings referenced failed bind of `192.168.50.3:3001` and `192.168.50.3:9000` with `cannot assign requested address`.

**Resolution:** Recreated containers with IPv4 `0.0.0.0` port mappings, preserved volumes and network membership, and retained restricted Docker `DOCKER-USER` inbound rules. Reboot test then showed Kuma healthy, Portainer running and successful HTTP checks (`200` and `302`).

## Docker incident: Kuma falsely marked services DOWN

**Symptom:** LAN browser could reach Pi-hole and Portainer, but checks originating from Kuma timed out.

**Cause identified:** Docker-side source addresses did not satisfy the host's LAN-only filtering; access through the host-published Portainer address was also problematic.

**Resolution:** Created user-defined `monitoring` bridge and checked Portainer at `http://portainer:9000`. Added narrowly targeted access from the monitoring subnet to host Pi-hole web ports. Validated both via `curl` from inside Kuma.

## Useful diagnostics

```sh
docker ps -a
docker inspect uptime-kuma --format '{{.State.Status}} {{.State.Error}}'
docker logs --tail 50 uptime-kuma
sudo iptables -L DOCKER-USER -n -v --line-numbers
sudo systemctl status docker --no-pager
```

Tests shown here document observed behavior at the time; they do not establish a perpetual security guarantee.

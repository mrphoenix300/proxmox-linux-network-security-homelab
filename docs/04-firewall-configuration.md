# 04 — Firewall design

This project uses several **different** enforcement points. They should not be treated as one interchangeable firewall.

## Proxmox host firewall

Datacenter options reported: firewall enabled, input DROP, output ACCEPT, forward ACCEPT. On node `pve`, allow rules from `192.0.2.0/24` were added for TCP 8006 (management UI), TCP 22 (SSH), and ICMP troubleshooting. Administrative connectivity was confirmed after these changes.

## Proxmox Debian VM firewall

For VM 100, Proxmox-level inbound allow rules from `192.0.2.0/24` cover TCP 22, TCP/UDP 53, TCP 80, TCP 443, UDP 123, TCP 3001 and TCP 9000; inbound policy DROP, outbound ACCEPT.

## Debian UFW

Incoming DENY, outgoing ALLOW, routed DENY. Host LAN allows:

| Source | Port/protocol |
|---|---|
| `192.0.2.0/24` | TCP 22, 53, 80, 443; UDP 53, 123 |
| `203.0.113.0/24` | TCP 80, 443 **to `192.0.2.3` only**, for Kuma-to-Pi-hole monitoring |

IPv6 handling is enabled in UFW, but complete IPv6 external exposure testing was not performed.

## Docker `DOCKER-USER`

Installed IPv4 forwarding rules, checked after reboot:

```text
1 ACCEPT  in=ens18  ctstate ESTABLISHED,RELATED
2 ACCEPT  in=ens18  src=192.0.2.0/24  ctstate NEW
3 DROP    in=ens18  ctstate NEW
4 RETURN  everything else
```

This restricts **new forwarded IPv4 connections arriving on `ens18`** while allowing home-LAN access. It does not, alone, filter every Docker pathway or make services safe to expose to an arbitrary network. The Docker published ports listen on all host IPv4 interfaces, so the enforcement must remain enabled.

To survive Docker restarts, a root-owned script `/usr/local/sbin/docker-lan-firewall` was wired into a Docker service drop-in `/etc/systemd/system/docker.service.d/20-lan-firewall.conf`:

```ini
[Service]
ExecStartPost=/usr/local/sbin/docker-lan-firewall
```

The script removes duplicate copies of these three rules and inserts them in the defined order. Exact production script contents are intentionally not copied here as an executable deployment tool; refer to the existing host script if performing maintenance.

## Read-only checks

```sh
sudo ufw status verbose
sudo iptables -L DOCKER-USER -n -v --line-numbers
sudo systemctl cat docker
```

**Change safety:** maintain a working Proxmox console and a second SSH session before modifying input policies, routing, or Docker firewall rules. Test from both LAN and, when appropriate, an authorized external network before claiming public exposure is blocked.

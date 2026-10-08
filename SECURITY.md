# Security and publication checklist

This repository is **documentation**, not a set of automated rules to deploy blindly. Do not interpret a functional ping or service test as a comprehensive security assessment.

## Public version privacy policy

Private IP addresses in this repository have been changed to **illustrative examples** (`192.168.50.0/24`, `10.50.10.0/24`, `172.20.0.0/16`). These values are **not** a runnable copy of the deployed network. Never upload original system configuration, unredacted screenshots, or raw backups.

## Keep out of GitHub

- Public WAN IPs, dynamic DNS records and router account identifiers when not needed
- Passwords, SSH private keys, WireGuard/Tailscale keys, recovery codes and API tokens
- Unredacted pfSense configuration exports or router backup files
- Sensitive Pi-hole query logs or Portainer environment details
- Screenshots containing active sessions, secrets, private metadata or identifiers
- Actual Docker volumes / SQLite databases / binary system backups

## Scope and limits

- No VPN or router port forwarding was configured for the new lab.
- LAN firewall allowlists use RFC1918 addresses; local-only network policy is not equivalent to proof of external non-reachability.
- Docker published services listen on all IPv4 interfaces, protected by a set of forwarding/firewall rules that require periodic auditing.
- The temporary host IP on `vmbr1` enables management of the isolated pfSense LAN; it does not, by itself, enable IP forwarding.
- Consider a separate management user with SSH keys and least-privilege privileges as future work.
- Independent validation of router UPnP, pre-existing forwarding rules, IPv6 exposure, and external reachability is outstanding.

## Before adding screenshots

1. Crop to the relevant UI section.
2. Redact user names, tokens, MAC addresses, public IPs and serial numbers where not necessary.
3. Confirm there are no open passwords, browser sessions or credentials in view.
4. Review image metadata before publishing.

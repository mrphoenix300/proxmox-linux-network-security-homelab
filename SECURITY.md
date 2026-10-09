# Security and publication checklist

This repository is **documentation**, not a set of automated rules to deploy blindly. Do not interpret a functional ping or service test as a comprehensive security assessment.

## Public version privacy policy

All environment IP addresses use **RFC 5737 documentation-only TEST-NET ranges** (`192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24`). These are illustrative and not suitable for actual deployment. Do not run commands containing them verbatim. Never publish original configuration exports, raw backups or unredacted screenshots.

## Keep out of GitHub

- Public WAN IPs, dynamic DNS records and router account identifiers when not needed
- Passwords, SSH private keys, WireGuard/Tailscale keys, recovery codes and API tokens
- Unredacted pfSense configuration exports or router backup files
- Sensitive Pi-hole query logs or Portainer environment details
- Screenshots containing active sessions, secrets, private metadata or identifiers
- Actual Docker volumes / SQLite databases / binary system backups

## Scope and limits

- No VPN or router port forwarding was configured for the new lab.
- Real LAN firewall allowlists use private IPv4 addresses; local-only network policy is not equivalent to proof of external non-reachability.
- Docker published services listen on all IPv4 interfaces, protected by a set of forwarding/firewall rules that require periodic auditing.
- The temporary host IP on `vmbr1` enables management of the isolated pfSense LAN; it does not, by itself, enable IP forwarding.
- Consider a separate management user with SSH keys and least-privilege privileges as future work.
- Independent validation of router UPnP, pre-existing forwarding rules, IPv6 exposure, and external reachability is outstanding.

## Before adding screenshots

1. Crop to the relevant UI section.
2. Redact user names, tokens, MAC addresses, public IPs and serial numbers where not necessary.
3. Confirm there are no open passwords, browser sessions or credentials in view.
4. Review image metadata before publishing.

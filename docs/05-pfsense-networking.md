# 05 — pfSense routing and isolated lab LAN

## VM and installation

- VM 101 `pfsense-lab`; pfSense CE `2.9.0-RELEASE`
- 1 vCPU, 1 GiB RAM, 8 GiB virtual disk
- SeaBIOS; UFS and GPT partitioning
- WAN `vtnet0` / Proxmox `net0` -> `vmbr0`
- LAN `vtnet1` / Proxmox `net1` -> `vmbr1`

## Address plan

| Interface | Address | Role |
|---|---|---|
| WAN | DHCP, observed `192.0.2.16/24` | Upstream home LAN |
| LAN | `198.51.100.1/24` | Isolated lab network |
| Alpine | DHCP, observed `198.51.100.100/24` | Test client |

Configured LAN DHCP pool: `198.51.100.100`–`198.51.100.199`. WAN **Block private networks** disabled due to private RFC1918 addressing on the real home network (shown here as anonymized TEST-NET addresses); **Block bogon networks** enabled in the wizard.

General pfSense setup: hostname `pfsense-lab`, domain `home.arpa`, DNS resolvers `1.1.1.1` and `1.0.0.1`, time zone `Europe/Athens`. Default admin password was changed.

## LAN behavior verified

The Alpine test client obtained a lease from pfSense, pinged `198.51.100.1`, reached Internet IPs and resolved `google.com`. No changes were made to the Debian VM's `vmbr0` connection; pfSense only handled the isolated test lab.

## Admin GUI through SSH tunnel

Temporary Proxmox-side interface address:

```sh
# Run on Proxmox host; temporary until reboot
ip addr add 198.51.100.2/24 dev vmbr1
```

From the Pop!_OS desktop:

```sh
ssh -N -o ExitOnForwardFailure=yes   -L 127.0.0.1:8443:198.51.100.1:443 <proxmox-user>@192.0.2.10
```

Local browser: `https://localhost:8443`. The SSH terminal must remain open. This was used successfully to complete the web setup wizard, and **no router port forward** was created for this purpose.

## Deferred remote access

WireGuard remote access was discussed but **not configured**; Tailscale was only considered as an option. No pfSense VPN port forwarding was opened on the home router as part of this project.

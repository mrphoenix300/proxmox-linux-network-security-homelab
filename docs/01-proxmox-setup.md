# 01 — Proxmox setup and virtualization

## Hardware

- Lenovo ThinkPad X201, 4 GB RAM, 128 GB SSD
- Proxmox VE hypervisor; managed from a Pop!_OS desktop
- Home router / gateway: `192.0.2.1`
- Proxmox management: `192.0.2.10`

## VMs

| VM | Purpose | Assigned memory | CPU | Disk | Network |
|---|---|---:|---:|---:|---|
| 100 `debian-lab` | Linux DNS/containers | 512 MiB | 1 core | 15 GiB (roadmap) | `vmbr0` |
| 101 `pfsense-lab` | pfSense CE 2.9.0 | 1024 MiB | 1 core | 8 GiB | `vmbr0`, `vmbr1` |
| 102 `alpine-test` | Small testing VM (live ISO) | 256 MiB | 1 core | 2 GiB | `vmbr1` |

> Debian disk size is from the original roadmap, not re-verified from a current VM hardware screenshot. Alpine tests were from a live environment; their in-guest configuration is not known to persist across reboot.

## Virtual switching

- `vmbr0`: physical/home LAN; Proxmox management and Debian; pfSense WAN.
- `vmbr1`: internal-only Linux bridge without physical bridge port; pfSense LAN and Alpine client.
- pfSense WAN uses DHCP (observed `192.0.2.16/24`); pfSense LAN uses `198.51.100.1/24`.

## Resource constraints

A check while Debian was stopped showed ~771 MiB available memory on the Proxmox host, so a 256 MiB Alpine test VM was chosen. Available memory varies; check it before powering on all guests:

```sh
free -h
```

## Startup

A reboot of **Debian VM** successfully recovered Docker services and persistent firewall rules. Full host shutdown/power-on and automated VM start ordering were not separately validated as part of the recorded checks.

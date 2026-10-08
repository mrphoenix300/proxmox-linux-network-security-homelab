# 07 — Backup and disaster recovery (next stage)

## Current evidence

Local Docker data archives were created on the Debian VM in `/root/docker-backup/`:

- `uptime-kuma-data.tar.gz`
- `portainer-data.tar.gz`

The containers were recreated without losing their original named volumes. This is useful, but it is **not** a substitute for a tested independent backup and restore procedure.

## Planned work (not yet complete)

- [ ] Create a full Proxmox backup of Debian VM 100.
- [ ] Export pfSense configuration (handle as secret; do not commit unredacted XML).
- [ ] Back up the pfSense VM or document a tested rebuild procedure.
- [ ] Copy backups off the same physical SSD.
- [ ] Verify restore using a **different VM ID on an isolated bridge** to avoid duplicate IPs, DHCP and DNS conflicts.
- [ ] Document RTO/RPO targets appropriate to a personal lab.

## Safe restore considerations

Do not boot a restored pfSense VM on `vmbr0` / `vmbr1` alongside the live one with the same settings unless you have deliberately isolated its interfaces. A second DHCP server or duplicated IP could disrupt the lab. Similarly, do not start a cloned Pi-hole on the home LAN until its IP and DNS behavior are reviewed.

Once the first restore test is done, record commands, dates, duration and lessons learned here.

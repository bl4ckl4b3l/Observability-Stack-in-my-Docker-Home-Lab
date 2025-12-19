## Observability-Stack-in-my-Docker-Home-Lab

## The Goal ##
The goal: Centralized metrics + logging, proactive monitoring, and learning real-world troubleshooting (I/O bottlenecks, container stability, resource tuning). This project has been gold for deepening Linux/Docker/Proxmox knowledge — perfect for infra/sec ops roles.

## Key Components & Setup ##

## Docker-Compose Structure:
Separate networks: observability (internal metrics/logging), homelab (app bridge), proxy (Traefik external), macvlan_net (Pi-hole static IP).
Volumes: Local SSD (/var/lib/observability) for write-heavy data (Prometheus TSDB, Grafana DB, Loki chunks) → fast I/O, no NFS latency.
TrueNAS NFS for read-heavy (Jellyfin media, some logs/DB backups).

## Prometheus:
Scrapes Node Exporter (host), cAdvisor (containers), PVE Exporter (Proxmox).
Config: Mounted ./prometheus.yml → /etc/prometheus/prometheus.yml.
Tuned with --storage.tsdb.retention.time=7d and --storage.tsdb.retention.size=5GB → prevents gradual storage/I/O growth.
Enabled --web.enable-lifecycle for hot-reloads.
Fixed startup errors by aligning config path with image defaults.

## Grafana:
Dashboards for host/container metrics (import Node Exporter Full #1860, cAdvisor).
Data sources: Prometheus + Loki.
Secured behind Traefik with local-only middleware.

## Loki + Promtail:
Loki: TSDB mode (v13 schema), 210d retention (adjustable), compaction enabled.
Promtail: Scrapes Docker containers (via socket) + system logs → structured labels (container, project).
Pipeline: Docker + JSON stages for clean parsing.

## Pi-hole Integration:
Primary DNS/ad-blocker on macvlan for direct network access.
Query history limited (planning FTL_MAXDBDAYS=30) → controls DB growth.
Web UI behind Traefik.

## Docker-Wide Improvements:
Daemon config: Switched logging driver to json-file with rotation (max-size: 10m, max-file: 3) → eliminated systemd-journald I/O spikes.
Journald capped (SystemMaxUse=100M) → no more log explosions.
Best practices applied: init: true (zombie reaping), resource limits (cgroups), read-only config mounts.

## Major Troubleshooting Wins ##
Started with recurring issues:

High load average + I/O wait despite low CPU → Pi-hole slowness, Jellyfin hangs.
Prometheus zombie process → forced removes.
NFS bind-mount races on shutdown.
Gradual overload from logging/metric bloat.

## Root causes & fixes:

systemd-journald bursts (Docker default driver flooding journals) → capped + switched to rotated json-file.
Prometheus/Loki growth → retention limits + local SSD storage.
Pi-hole DB bloat → history pruning env var.
Config mismatches → aligned Prometheus paths/flags.

## Result:
System now stable (load < cores, I/O wait <5%, no reboots needed). Monitoring self-heals issues proactively!

[Traefik Dashboard]<img width="1123" height="386" alt="image" src="https://github.com/user-attachments/assets/159aea30-4176-4b47-9b52-7a309ba20c73" />
[Node Exporter]<img width="1111" height="436" alt="image" src="https://github.com/user-attachments/assets/4c17af45-32f9-47de-9bcb-f3d6c4789745" />

# Homelab-Real-time-Monitoring-with-Netdata
Learning infrastructure security, attack simulation visibility and new tools

## Main goals:
- Build a stable Proxmox-based virtualization platform
- Implement distributed, real-time monitoring with **Netdata**
- Centralize metrics on a Raspberry Pi (parent node)
- Gain visibility into both hypervisor and guest VMs as well as primary management PC
- Observe resource impact of simulated attacks / red-team exercises


## Hardware / Software Used

| Component               | Hardware / OS                          | Role                              |
|-------------------------|----------------------------------------|-----------------------------------|
| Proxmox nodes           | 2× physical servers (x86_64)           | Main virtualization cluster       |
| Quorum node             | Raspberry Pi 5 (ARM64)                 | Proxmox quorum + Netdata parent   |
| Primary server VM       | Ubuntu 24.04 LTS                       | Main services (monitoring target) |
| Windows machine         | Windows 11                             | Windows guest monitoring          |
| Monitoring              | Netdata (latest stable + some nightly) | Real-time metrics & anomaly detection |
| Networking              | 192.168.x.x/24                         | Lab internal network              |

## Key Learning Outcomes / Resume Highlights

- Designed & deployed a **distributed Netdata architecture** (parent ↔ children)
- Configured **UUID-based authentication** and IP restrictions for streaming
- Implemented **centralized alerting** (health checks only on parent)
- Troubleshot ARM64 + x86 compatibility issues in Proxmox + Netdata
- Managed third-party repository GPG key problems on Ubuntu (Crowdsec related)
- Practiced **principle of least privilege** (non-root users, sudo, restricted access)
- Created **layered visibility** (hypervisor + guest OS monitoring)
- Documented configuration backups and change management process

# Initial Dashboard
Live servers, real-time resource bars, alerts indicators
<img width="1914" height="763" alt="image" src="https://github.com/user-attachments/assets/a09da90e-8c08-4563-88e1-3942f35031e5" />

# Ubuntu Drill In Data
Seeing authentik, immich, jellyfin, postgres, etc. fighting for resources — exactly what I wanted
<img width="1304" height="656" alt="image" src="https://github.com/user-attachments/assets/e675cd7a-3faa-4145-bc38-00999d8d6bcf" />

# Container Data from the Ubuntu VM
Guest-level visibility — top processes, cgroup limits, container/VM metrics, real application impact.
<img width="1593" height="875" alt="image" src="https://github.com/user-attachments/assets/5aff1c6e-023c-4bcf-838b-0c7a15b0ad1e" />

# Alerts
Netdata is already yelling at me about disk backlogs, cgroup limits, Docker health, and network storms — perfect for noticing when my "friendly" attack scripts start causing trouble.
<img width="1298" height="822" alt="image" src="https://github.com/user-attachments/assets/56d3360e-dec7-471f-9b73-014fa070ddc3" />

# Remediations
Real-time systemd/container logs — perfect for spotting failed services or weird startup behavior.
<img width="984" height="906" alt="image" src="https://github.com/user-attachments/assets/68d711d8-57c2-414b-af45-1cd718d9fc33" />


## Overall Summary – What I've Achieved

- Distributed monitoring fully operational (4+ nodes streaming to parent)
- Centralized dashboard with node switching, resource overviews, and multi-node comparison
- Proactive alerting — catching disk, CPU, network, and container issues early
- Deep guest visibility — application-level metrics inside VMs
- Log integration — real-time debugging and potential security monitoring

## Future Ideas

- Tune alerts (silence noisy ones, add custom rules)
- Add anomaly detection dashboards
- Integrate notifications (email/Discord/Slack)
- Document attack sims and observed metrics



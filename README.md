# wtlab - Homelab Core Infrastructure

## Overview

This project serves as documentation and the journey for my homelab setup; it will be continuously WIP as this lab will be for both personal use and learning new skills.

Currently, this homelab is currently built on a NUC running VMware ESXi 8; tentatively, the expansion of this project will include adding a NAS for storage and additional NUC(s) for redundancy. This setup features using 3 different VMs / repos all using docker compose, segmenting each stack from one another with the only dependency being the core infrastructure stack (this current repo). 

The goal of this setup is to be both scalable and portable; inspired by similar setups, I will be using NUCs and smaller smart switches so that this homelab can be setup anywhere without needing a large dedicated space (+reduced noise and power consumption).

#### TLDR:
Scalable and portable homelab setup split into 3 VMs/repos utilizing docker compose, all dependent on the current repo which is the core infrastructure

### Table of Contents

- [Overview](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#overview)
- [Components](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#components)
  - [Hardware](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#hardware)
    - [Servers](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#servers)
    - [Storage](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#storage)
    - [Networking](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#networking)
    - [Other](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#other)
  - [Repositories](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#repositories)
    - [Core Infrastructure](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#core-infrastructure-wtlab-core-infra)
    - [Monitor Stack](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#monitor-stack-wtlab-monitor-stack)
    - [Media Stack](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#media-stack-wtlab-media-stack)
- [Setup](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#setup)
  - [Prerequisites](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#prerequisites)
  - [Deployment](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#deployment)
  - [Post-Deployment Config](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#post-deployment-config)
- [Roadmap](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#roadmap)

## Components

### Hardware

#### Servers

- Lenovo Thinkcentre M700
   - CPU: i5-6500T @ 2.50GHz
   - RAM: 32GB
   - SSD: 1TB
   - OS: VMware ESXi-8.0.0-20513097-standard

| Virtual Machine | Repository / Docker Compose Stack | OS | CPU | RAM | STORAGE
|:--------------- | :-------------------------------: | -- | --- | --- | ------ |
| prod-core-01 | wtlab-core-infra | Ubuntu 22.04.5 LTS | 2 vCPUs | 4 GB | 25 GB
| prod-monitor-01 | [monitor-stack](https://github.com/wtluong/wtlab-monitor-stack) | Ubuntu 22.04.5 LTS | 4 vCPUs | 8 GB | 75 GB
| prod-media-01 | media-stack | Ubuntu 22.04.5 LTS | 6 vCPUs | 8 GB | 400 GB
     
The first NUC of this project, depending on the direction of this project this will likely be part of a NUC cluster.


#### Storage

- **Future Expansion**

#### Networking

- xFinity WiFi Gateway
   - currently locked in using plan due to cheaper rates using their router vs own router, unfortunately restricts a lot of homelab capabilities e.g. segmented VLANs, setting custom DNS, port forwarding due to technical issues with the device; tentatively want to replace once on a new WiFi plan
  
#### Other

- Leaving this category for additional appliances that might not fit under other categories

### Repositories

#### Core Infrastructure: wtlab-core-infra

- [Portainer](https://www.portainer.io/)
- [Pi-hole](https://pi-hole.net/)
- [Nginx Proxy Manager](https://nginxproxymanager.com/)
- [Homer](https://github.com/bastienwirtz/homer)
- [Uptime Kuma](https://github.com/louislam/uptime-kuma)
- [Tailscale](https://tailscale.com/)

#### Monitor Stack: [wtlab-monitor-stack](https://github.com/wtluong/wtlab-monitor-stack)

- Prometheus
   - Alertmanager
   - Node Exporter
   - cAdvisor (Container Advisor)
- Loki
   - Promtail
- Grafana

#### Media Stack: wtlab-media-stack
- Currently Planning

## Setup

###  Prerequisites

NOTE: This deployment was completed on **Ubuntu 22.04.5 LTS**; setup may vary if using another linux distribution/OS.
- [Docker Compose](https://docs.docker.com/compose/)
- [Tailscale Account](https://login.tailscale.com/start) (not required, may skip if only accessing via LAN / other VPN method is being used)
- Disable DNS to prevent Pi-hole port 53 conflict error (this stops Pi-hole from starting)
```
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf

echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
echo "nameserver 8.8.4.4" | sudo tee -a /etc/resolv.conf
```
#TODO: Automate this step; set `[Resolve] DNSStubListener=no` instead of nuking config (?)
  
  
### Deployment

1. Create .env file
   
e.g.
.env

```
# Timezone
TIMEZONE=America/New_York

# Pi-hole
PIHOLE_PASSWORD=your-secure-password-here

```

#TODO: Create a **.env.example** - once completed, this step will instead be: *1. Rename **.env.example** to **.env** and fill in the variables*
  
  
2. Create custom dns file for pihole and dns entries for services

e.g.
etc-dnsmasq.d

```
address=/homelab.local/192.168.1.xxx  # Your VM IP
address=/portainer.local/192.168.1.xxx
address=/pihole.local/192.168.1.xxx
address=/homer.local/192.168.1.xxx
```

#TODO: Create a **etc-dnsmasq.d.example** - once completed, this step will instead be: *2. **Rename etc-dnsmasq.d.example** to **etc-dnsmasq.d** and fill in the variables* ; potentially reference .env to reduce this step (?)
  
  
### Post-Deployment Config:

#TODO: Go into more detail for each setup; have password setup in .env file if possible

### Configure Portainer
```
1. Open: http://your-vm-ip:9000
2. Create admin account
```

### Configure Pi-hole
```
1. Open: http://your-vm-ip:8080/admin
2. Login with password from .env file
3. Go to Settings → DNS
4. Add custom DNS entries for your services
5. Set as your router's DNS server (or manually on devices)
```

### Configure Nginx Proxy Manager
```
1. Open: http://your-vm-ip:81
2. Default login: admin@example.com / changeme
3. Change password immediately
4. Set up proxy hosts for your services
```

### Access Homer Dashboard
```
1. Open: http://your-vm-ip:8082
2. Edit config if needed: docker compose restart homer
```

### Configure Uptime Kuma
```
1. Open: http://your-vm-ip:3001
2. Create admin account
3. Add monitors for each service:
   - Portainer: HTTP monitor on port 9000
   - Pi-hole: HTTP monitor on port 8080
   - Nginx PM: HTTP monitor on port 81
```
  
## Roadmap

- [x] [Core Infrastructure](https://github.com/wtluong/wtlab-core-infra?tab=readme-ov-file#overview)
   - [ ] Automation / .env variables for less manual setup
   - [ ] Backup Strategy
- [ ] [Monitor Stack](https://github.com/wtluong/wtlab-monitor-stack)
- [ ] Media Stack - **Planning**
- [ ] Ansible for VM Management - **Future expansion**
- [ ] Terraform for VM Deployment - **Future expansion**
- [ ] Homelab Expansion - **Future expansion**
   - [ ] NAS - Media & Backup Storage 
   - [ ] Second NUC - High Availability

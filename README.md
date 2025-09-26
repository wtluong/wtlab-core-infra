# wtlab - Homelab Core Infrastructure

## Overview

### Table of Contents

- [Overview](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#overview)
- [Components](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#components)
- [Setup](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#setup)
  - [Prerequisites](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#prerequisites)
  - [Deployment](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#deployment)
  - [Post-Deployment Config](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#post-deployment-config)
- [Roadmap](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#roadmap)

## Components

### Core Infrastructure: wtlab-core-infra

- Portainer
- Pi-hole
- Nginx Proxy Manager
- Homer
- Uptime Kuma
- Tailscale

### Monitor Stack: [wtlab-monitor-stack](https://github.com/wtluong/wtlab-monitor-stack)

- Prometheus
   - Alertmanager
   - Node Exporter
   - cAdvisor (Container Advisor)
- Loki
   - Promtail
- Grafana

### Media Stack: wtlab-media-stack - Planned

## Setup

###  Prerequisites

NOTE: This deployment was completed on **Ubuntu 24.04.3 LTS**; setup may vary if using another linux distribution/OS.
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

- [x] [Core Infrastructure](https://github.com/wtluong/wtlab-core-infra/edit/main/README.md#overview)
   - [ ] Automation / .env variables for less manual setup
   - [ ] Backup Strategy
- [ ] [Monitor Stack](https://github.com/wtluong/wtlab-monitor-stack)
- [ ] Media Stack - **Planning**
- [ ] Ansible for VM Management - **Future expansion**
- [ ] Terraform for VM Deployment - **Future expansion**
- [ ] Homelab Expansion - **Future expansion**
   - [ ] NAS - Media & Backup Storage 
   - [ ] Second NUC - High Availability

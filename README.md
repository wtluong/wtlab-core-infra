WIP

Prereqs:

1. Docker Compose

2. Disable dns for it prevent pi-hole port 53 conflict error, thus stopping it from starting

sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf

echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
echo "nameserver 8.8.4.4" | sudo tee -a /etc/resolv.conf

3. create .env file
e.g.
.env
```
# Timezone
TIMEZONE=America/New_York

# Pi-hole
PIHOLE_PASSWORD=your-secure-password-here

# WireGuard
WIREGUARD_SERVERURL=your-domain.duckdns.org
WIREGUARD_PEERS=3
```

4. create custom dns file for pihole and dns entries for services

e.g.
etc-dnsmasq.d
```
address=/homelab.local/192.168.1.xxx  # Your VM IP
address=/portainer.local/192.168.1.xxx
address=/pihole.local/192.168.1.xxx
address=/homer.local/192.168.1.xxx
```

### Post-Deployment Config:

configure Portainer
```
1. Open: http://your-vm-ip:9000
2. Create admin account
3. Select "Docker Standalone"
4. Connect to local Docker instance
```

### Step 2: Configure Pi-hole
```
1. Open: http://your-vm-ip:8080/admin
2. Login with password from .env file
3. Go to Settings → DNS
4. Add custom DNS entries for your services
5. Set as your router's DNS server (or manually on devices)
```

### Step 3: Configure WireGuard
```
1. Check logs: docker compose logs wireguard
2. QR codes will be generated in: ./wireguard/peer_xxx/
3. Scan QR codes with WireGuard mobile app
4. Test VPN connection
```

### Step 4: Configure Nginx Proxy Manager
```
1. Open: http://your-vm-ip:81
2. Default login: admin@example.com / changeme
3. Change password immediately
4. Set up proxy hosts for your services (optional for now)
```

### Step 5: Access Homer Dashboard
```
1. Open: http://your-vm-ip:8082
2. Edit config if needed: docker compose restart homer
3. Bookmark this as your homelab homepage
```

### Step 6: Configure Uptime Kuma
```
1. Open: http://your-vm-ip:3001
2. Create admin account
3. Add monitors for each service:
   - Portainer: HTTP monitor on port 9000
   - Pi-hole: HTTP monitor on port 8080
   - Nginx PM: HTTP monitor on port 81
```

---

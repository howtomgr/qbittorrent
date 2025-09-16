# qbittorrent Installation Guide

qbittorrent is a free and open-source BitTorrent client. qBittorrent provides a free alternative to µTorrent with a feature-rich interface

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for downloads
  - Network: BitTorrent protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default qbittorrent port)
  - Peer ports 6881
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install qbittorrent
sudo dnf install -y qbittorrent

# Enable and start service
sudo systemctl enable --now qbittorrent

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
qbittorrent --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install qbittorrent
sudo apt install -y qbittorrent

# Enable and start service
sudo systemctl enable --now qbittorrent

# Configure firewall
sudo ufw allow 8080

# Verify installation
qbittorrent --version
```

### Arch Linux

```bash
# Install qbittorrent
sudo pacman -S qbittorrent

# Enable and start service
sudo systemctl enable --now qbittorrent

# Verify installation
qbittorrent --version
```

### Alpine Linux

```bash
# Install qbittorrent
apk add --no-cache qbittorrent

# Enable and start service
rc-update add qbittorrent default
rc-service qbittorrent start

# Verify installation
qbittorrent --version
```

### openSUSE/SLES

```bash
# Install qbittorrent
sudo zypper install -y qbittorrent

# Enable and start service
sudo systemctl enable --now qbittorrent

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
qbittorrent --version
```

### macOS

```bash
# Using Homebrew
brew install qbittorrent

# Start service
brew services start qbittorrent

# Verify installation
qbittorrent --version
```

### FreeBSD

```bash
# Using pkg
pkg install qbittorrent

# Enable in rc.conf
echo 'qbittorrent_enable="YES"' >> /etc/rc.conf

# Start service
service qbittorrent start

# Verify installation
qbittorrent --version
```

### Windows

```bash
# Using Chocolatey
choco install qbittorrent

# Or using Scoop
scoop install qbittorrent

# Verify installation
qbittorrent --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/qbittorrent

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
qbittorrent --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable qbittorrent

# Start service
sudo systemctl start qbittorrent

# Stop service
sudo systemctl stop qbittorrent

# Restart service
sudo systemctl restart qbittorrent

# Check status
sudo systemctl status qbittorrent

# View logs
sudo journalctl -u qbittorrent -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add qbittorrent default

# Start service
rc-service qbittorrent start

# Stop service
rc-service qbittorrent stop

# Restart service
rc-service qbittorrent restart

# Check status
rc-service qbittorrent status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'qbittorrent_enable="YES"' >> /etc/rc.conf

# Start service
service qbittorrent start

# Stop service
service qbittorrent stop

# Restart service
service qbittorrent restart

# Check status
service qbittorrent status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start qbittorrent
brew services stop qbittorrent
brew services restart qbittorrent

# Check status
brew services list | grep qbittorrent
```

### Windows Service Manager

```powershell
# Start service
net start qbittorrent

# Stop service
net stop qbittorrent

# Using PowerShell
Start-Service qbittorrent
Stop-Service qbittorrent
Restart-Service qbittorrent

# Check status
Get-Service qbittorrent
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream qbittorrent_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name qbittorrent.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name qbittorrent.example.com;

    ssl_certificate /etc/ssl/certs/qbittorrent.example.com.crt;
    ssl_certificate_key /etc/ssl/private/qbittorrent.example.com.key;

    location / {
        proxy_pass http://qbittorrent_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName qbittorrent.example.com
    Redirect permanent / https://qbittorrent.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName qbittorrent.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/qbittorrent.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/qbittorrent.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend qbittorrent_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/qbittorrent.pem
    redirect scheme https if !{ ssl_fc }
    default_backend qbittorrent_backend

backend qbittorrent_backend
    balance roundrobin
    server qbittorrent1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R qbittorrent:qbittorrent /etc/qbittorrent
sudo chmod 750 /etc/qbittorrent

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status qbittorrent

# View logs
sudo journalctl -u qbittorrent -f

# Monitor resource usage
top -p $(pgrep qbittorrent)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/qbittorrent"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/qbittorrent-backup-$DATE.tar.gz" /etc/qbittorrent /var/lib/qbittorrent

echo "Backup completed: $BACKUP_DIR/qbittorrent-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop qbittorrent

# Restore from backup
tar -xzf /backup/qbittorrent/qbittorrent-backup-*.tar.gz -C /

# Start service
sudo systemctl start qbittorrent
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u qbittorrent -n 100
sudo tail -f /var/log/qbittorrent/qbittorrent.log

# Check configuration
qbittorrent --version

# Check permissions
ls -la /etc/qbittorrent
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep qbittorrent)

# Check disk I/O
iotop -p $(pgrep qbittorrent)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  qbittorrent:
    image: qbittorrent:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/qbittorrent
      - ./data:/var/lib/qbittorrent
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update qbittorrent

# Debian/Ubuntu
sudo apt update && sudo apt upgrade qbittorrent

# Arch Linux
sudo pacman -Syu qbittorrent

# Alpine Linux
apk update && apk upgrade qbittorrent

# openSUSE
sudo zypper update qbittorrent

# FreeBSD
pkg update && pkg upgrade qbittorrent

# Always backup before updates
tar -czf /backup/qbittorrent-pre-update-$(date +%Y%m%d).tar.gz /etc/qbittorrent

# Restart after updates
sudo systemctl restart qbittorrent
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/qbittorrent

# Clean old logs
find /var/log/qbittorrent -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/qbittorrent
```

## Additional Resources

- Official Documentation: https://docs.qbittorrent.org/
- GitHub Repository: https://github.com/qbittorrent/qbittorrent
- Community Forum: https://forum.qbittorrent.org/
- Best Practices Guide: https://docs.qbittorrent.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.

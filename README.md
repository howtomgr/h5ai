# h5ai Installation Guide

h5ai is a free and open-source modern file indexer. h5ai provides a modern web interface for directory listings with file previews

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
  - RAM: 128MB minimum
  - Storage: 50MB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default h5ai port)
  - None
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

# Install h5ai
sudo dnf install -y h5ai

# Enable and start service
sudo systemctl enable --now h5ai

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
h5ai --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install h5ai
sudo apt install -y h5ai

# Enable and start service
sudo systemctl enable --now h5ai

# Configure firewall
sudo ufw allow 80

# Verify installation
h5ai --version
```

### Arch Linux

```bash
# Install h5ai
sudo pacman -S h5ai

# Enable and start service
sudo systemctl enable --now h5ai

# Verify installation
h5ai --version
```

### Alpine Linux

```bash
# Install h5ai
apk add --no-cache h5ai

# Enable and start service
rc-update add h5ai default
rc-service h5ai start

# Verify installation
h5ai --version
```

### openSUSE/SLES

```bash
# Install h5ai
sudo zypper install -y h5ai

# Enable and start service
sudo systemctl enable --now h5ai

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
h5ai --version
```

### macOS

```bash
# Using Homebrew
brew install h5ai

# Start service
brew services start h5ai

# Verify installation
h5ai --version
```

### FreeBSD

```bash
# Using pkg
pkg install h5ai

# Enable in rc.conf
echo 'h5ai_enable="YES"' >> /etc/rc.conf

# Start service
service h5ai start

# Verify installation
h5ai --version
```

### Windows

```bash
# Using Chocolatey
choco install h5ai

# Or using Scoop
scoop install h5ai

# Verify installation
h5ai --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/h5ai

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
h5ai --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable h5ai

# Start service
sudo systemctl start h5ai

# Stop service
sudo systemctl stop h5ai

# Restart service
sudo systemctl restart h5ai

# Check status
sudo systemctl status h5ai

# View logs
sudo journalctl -u h5ai -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add h5ai default

# Start service
rc-service h5ai start

# Stop service
rc-service h5ai stop

# Restart service
rc-service h5ai restart

# Check status
rc-service h5ai status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'h5ai_enable="YES"' >> /etc/rc.conf

# Start service
service h5ai start

# Stop service
service h5ai stop

# Restart service
service h5ai restart

# Check status
service h5ai status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start h5ai
brew services stop h5ai
brew services restart h5ai

# Check status
brew services list | grep h5ai
```

### Windows Service Manager

```powershell
# Start service
net start h5ai

# Stop service
net stop h5ai

# Using PowerShell
Start-Service h5ai
Stop-Service h5ai
Restart-Service h5ai

# Check status
Get-Service h5ai
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream h5ai_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name h5ai.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name h5ai.example.com;

    ssl_certificate /etc/ssl/certs/h5ai.example.com.crt;
    ssl_certificate_key /etc/ssl/private/h5ai.example.com.key;

    location / {
        proxy_pass http://h5ai_backend;
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
    ServerName h5ai.example.com
    Redirect permanent / https://h5ai.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName h5ai.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/h5ai.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/h5ai.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend h5ai_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/h5ai.pem
    redirect scheme https if !{ ssl_fc }
    default_backend h5ai_backend

backend h5ai_backend
    balance roundrobin
    server h5ai1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R h5ai:h5ai /etc/h5ai
sudo chmod 750 /etc/h5ai

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status h5ai

# View logs
sudo journalctl -u h5ai -f

# Monitor resource usage
top -p $(pgrep h5ai)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/h5ai"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/h5ai-backup-$DATE.tar.gz" /etc/h5ai /var/lib/h5ai

echo "Backup completed: $BACKUP_DIR/h5ai-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop h5ai

# Restore from backup
tar -xzf /backup/h5ai/h5ai-backup-*.tar.gz -C /

# Start service
sudo systemctl start h5ai
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u h5ai -n 100
sudo tail -f /var/log/h5ai/h5ai.log

# Check configuration
h5ai --version

# Check permissions
ls -la /etc/h5ai
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep h5ai)

# Check disk I/O
iotop -p $(pgrep h5ai)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  h5ai:
    image: h5ai:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/h5ai
      - ./data:/var/lib/h5ai
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update h5ai

# Debian/Ubuntu
sudo apt update && sudo apt upgrade h5ai

# Arch Linux
sudo pacman -Syu h5ai

# Alpine Linux
apk update && apk upgrade h5ai

# openSUSE
sudo zypper update h5ai

# FreeBSD
pkg update && pkg upgrade h5ai

# Always backup before updates
tar -czf /backup/h5ai-pre-update-$(date +%Y%m%d).tar.gz /etc/h5ai

# Restart after updates
sudo systemctl restart h5ai
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/h5ai

# Clean old logs
find /var/log/h5ai -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/h5ai
```

## Additional Resources

- Official Documentation: https://docs.h5ai.org/
- GitHub Repository: https://github.com/h5ai/h5ai
- Community Forum: https://forum.h5ai.org/
- Best Practices Guide: https://docs.h5ai.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.

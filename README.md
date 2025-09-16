# buddy Installation Guide

buddy is a free and open-source CI/CD platform. Buddy provides on-premises CI/CD with visual pipeline builder

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
  - CPU: 2+ cores
  - RAM: 4GB minimum
  - Storage: 50GB for builds
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default buddy port)
  - Various service ports
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

# Install buddy
sudo dnf install -y buddy

# Enable and start service
sudo systemctl enable --now buddy

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
buddy --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install buddy
sudo apt install -y buddy

# Enable and start service
sudo systemctl enable --now buddy

# Configure firewall
sudo ufw allow 80

# Verify installation
buddy --version
```

### Arch Linux

```bash
# Install buddy
sudo pacman -S buddy

# Enable and start service
sudo systemctl enable --now buddy

# Verify installation
buddy --version
```

### Alpine Linux

```bash
# Install buddy
apk add --no-cache buddy

# Enable and start service
rc-update add buddy default
rc-service buddy start

# Verify installation
buddy --version
```

### openSUSE/SLES

```bash
# Install buddy
sudo zypper install -y buddy

# Enable and start service
sudo systemctl enable --now buddy

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
buddy --version
```

### macOS

```bash
# Using Homebrew
brew install buddy

# Start service
brew services start buddy

# Verify installation
buddy --version
```

### FreeBSD

```bash
# Using pkg
pkg install buddy

# Enable in rc.conf
echo 'buddy_enable="YES"' >> /etc/rc.conf

# Start service
service buddy start

# Verify installation
buddy --version
```

### Windows

```bash
# Using Chocolatey
choco install buddy

# Or using Scoop
scoop install buddy

# Verify installation
buddy --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/buddy

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
buddy --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable buddy

# Start service
sudo systemctl start buddy

# Stop service
sudo systemctl stop buddy

# Restart service
sudo systemctl restart buddy

# Check status
sudo systemctl status buddy

# View logs
sudo journalctl -u buddy -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add buddy default

# Start service
rc-service buddy start

# Stop service
rc-service buddy stop

# Restart service
rc-service buddy restart

# Check status
rc-service buddy status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'buddy_enable="YES"' >> /etc/rc.conf

# Start service
service buddy start

# Stop service
service buddy stop

# Restart service
service buddy restart

# Check status
service buddy status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start buddy
brew services stop buddy
brew services restart buddy

# Check status
brew services list | grep buddy
```

### Windows Service Manager

```powershell
# Start service
net start buddy

# Stop service
net stop buddy

# Using PowerShell
Start-Service buddy
Stop-Service buddy
Restart-Service buddy

# Check status
Get-Service buddy
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream buddy_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name buddy.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name buddy.example.com;

    ssl_certificate /etc/ssl/certs/buddy.example.com.crt;
    ssl_certificate_key /etc/ssl/private/buddy.example.com.key;

    location / {
        proxy_pass http://buddy_backend;
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
    ServerName buddy.example.com
    Redirect permanent / https://buddy.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName buddy.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/buddy.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/buddy.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend buddy_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/buddy.pem
    redirect scheme https if !{ ssl_fc }
    default_backend buddy_backend

backend buddy_backend
    balance roundrobin
    server buddy1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R buddy:buddy /etc/buddy
sudo chmod 750 /etc/buddy

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
sudo systemctl status buddy

# View logs
sudo journalctl -u buddy -f

# Monitor resource usage
top -p $(pgrep buddy)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/buddy"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/buddy-backup-$DATE.tar.gz" /etc/buddy /var/lib/buddy

echo "Backup completed: $BACKUP_DIR/buddy-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop buddy

# Restore from backup
tar -xzf /backup/buddy/buddy-backup-*.tar.gz -C /

# Start service
sudo systemctl start buddy
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u buddy -n 100
sudo tail -f /var/log/buddy/buddy.log

# Check configuration
buddy --version

# Check permissions
ls -la /etc/buddy
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
top -p $(pgrep buddy)

# Check disk I/O
iotop -p $(pgrep buddy)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  buddy:
    image: buddy:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/buddy
      - ./data:/var/lib/buddy
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update buddy

# Debian/Ubuntu
sudo apt update && sudo apt upgrade buddy

# Arch Linux
sudo pacman -Syu buddy

# Alpine Linux
apk update && apk upgrade buddy

# openSUSE
sudo zypper update buddy

# FreeBSD
pkg update && pkg upgrade buddy

# Always backup before updates
tar -czf /backup/buddy-pre-update-$(date +%Y%m%d).tar.gz /etc/buddy

# Restart after updates
sudo systemctl restart buddy
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/buddy

# Clean old logs
find /var/log/buddy -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/buddy
```

## Additional Resources

- Official Documentation: https://docs.buddy.org/
- GitHub Repository: https://github.com/buddy/buddy
- Community Forum: https://forum.buddy.org/
- Best Practices Guide: https://docs.buddy.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.

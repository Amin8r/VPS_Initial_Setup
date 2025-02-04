# Things to do after deploying a VPS
# Server Setup Instructions

This guide provides step-by-step instructions for setting up a VPS for the first time. Follow these steps to configure your server securely and efficiently.

## 1. Login via SSH

Connect to your server using SSH:

```sh
ssh root@IP_ADDRESS
```

Enter the password when prompted and press `Return`.

## 2. Set the Hostname

Edit the hostname file:

```sh
nano /etc/hostname
```

Enter your preferred hostname. If your server has a domain, use the full domain name; otherwise, use a custom name.

Update the `/etc/hosts` file:

```sh
nano /etc/hosts
```

Add the hostname alongside `127.0.1.1`:

```
127.0.1.1    server.domain.com server
```

## 3. Set DNS Servers

Edit the DNS configuration:

```sh
nano /etc/resolv.conf
```

Add the following lines:

```
nameserver 1.1.1.1
nameserver 8.8.8.8
```

## 4. Enable TCP BBR (for better network performance)

Edit the system configuration file:

```sh
nano /etc/sysctl.conf
```

Add these lines:

```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```

Apply the changes:

```sh
sysctl -p
```

## 5. Create a New User

Create a new user (replace `mammad` with your preferred username):

```sh
adduser mammad
```

Grant `sudo` privileges:

```sh
usermod -aG sudo mammad
```

## 6. Update the Server

Update system packages:

```sh
apt update && apt dist-upgrade -y
```

Reboot the server to apply updates:

```sh
reboot
```

## 7. Add SSH Keys for Secure Authentication

On your local machine, copy your SSH key to the server:

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub mammad@server.domain.com
```

Test the connection:

```sh
ssh mammad@server.domain.com
```

Alternatively, configure an SSH alias:

```sh
nano ~/.ssh/config
```

Add the following details:

```
Host server
  HostName 123.586.789.012
  User mammad
```

Now you can connect using:

```sh
ssh server
```

## 8. Disable Root Login for Security

Edit the SSH configuration file:

```sh
sudo nano /etc/ssh/sshd_config
```

Find the line `PermitRootLogin` and set it to `no`.
Restart the SSH service:

```sh
sudo systemctl restart ssh
```

> **Important:** Open a new terminal and test SSH access before closing the current session.

## 9. Set Timezone (Optional)

Set your preferred timezone (replace `Asia/Tehran` if needed):

```sh
sudo timedatectl set-timezone Asia/Tehran
```

## 10. Install Essential Packages

Install commonly used packages:

```sh
sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common htop git bash-completion rsync make zip -y
```

## 11. Configure UFW (Uncomplicated Firewall)

Install UFW:

```sh
sudo apt install ufw -y
```

Allow necessary ports:

```sh
sudo ufw allow 22/tcp
sudo ufw allow 80
sudo ufw allow 443
```

Enable UFW:

```sh
sudo ufw enable
```

## 12. Install and Configure Fail2ban (for Security)

Install Fail2ban:

```sh
sudo apt install fail2ban -y
```

Check if it is running:

```sh
systemctl status fail2ban
```

View Fail2ban logs:

```sh
sudo tail -f /var/log/fail2ban.log
```

## 13. Set Up Swap (For Low-Memory Servers)

If your VPS has limited RAM, create a swap file:

```sh
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Make it permanent:

```sh
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

Verify swap is active:

```sh
free -h
```

## 14. Enable Automatic Security Updates

Install and configure unattended-upgrades:

```sh
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
```

Check logs to confirm updates:

```sh
cat /var/log/unattended-upgrades/unattended-upgrades.log
```

## 15. System Monitoring Tools

Install `htop` and `glances`:

```sh
sudo apt install htop glances -y
htop
```

To run Glances:

```sh
glances
```

## 16. Ensure Critical Services Restart Automatically

Enable essential services to restart automatically:

```sh
sudo systemctl enable --now fail2ban
sudo systemctl enable --now ufw
```

Check service status:

```sh
sudo systemctl status fail2ban
sudo systemctl status ufw
```

## 17. Check SSH Login Attempts

To monitor failed SSH login attempts:

```sh
sudo journalctl -u ssh --no-pager | grep "Failed"
```

##


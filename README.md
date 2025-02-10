
# alpine-linux-server-setup-and-config

This repository documents the setup and configuration of an Alpine Linux server running on a Lenovo M93p. The server is used for hosting personal projects, web applications, and file storage.

## Table of Contents
1. [Overview](#overview)
2. [Initial Setup](#initial-setup)
3. [SSH Key Setup](#ssh-key-setup)
4. [Installing Core Services](#installing-core-services)
5. [Setting Up Version Control and Documentation](#setting-up-version-control-and-documentation)

---

## Overview
This project involves configuring an Alpine Linux server for personal use on a home network, focusing on security, documentation, and accessibility. The server will host Nginx, Fail2ban, and nftables as core services.

---

## Initial Setup

1. **Install Alpine Linux** on the Lenovo M93p server.
   - Completed initial system setup using `setup-alpine`.
   - Configured hostname, network, and timezone.
   - Set up SSH service for remote access using key and disabled passwords.

2. **Create a New User** on the server (optional for enhanced security).
   - This example retains root access until other configurations are completed.

---

## SSH Key Setup

1. **Generate an SSH Key** on an Arch Linux PC to allow key-based SSH access to the Alpine server.
   ```bash
   ssh-keygen -t rsa -b 4096 -C "archDean@local"
   ```

2. **Copy the Public Key to the Alpine Server**:
   - Used a USB drive to transfer the public key to the server, due to SSH key-only configuration on the server.

3. **Add the Public Key to Authorized Keys**:
   - Created the `.ssh` directory and added the public key to `~/.ssh/authorized_keys` on the server.

4. **Verify SSH Access**:
   - Tested key-based login from the Arch Linux PC to the Alpine server.

---

## Installing Core Services

1. **Install Nginx** (Web Server):
   ```bash
   apk add nginx
   ```
   - Started the Nginx service and enabled it to run on boot.

2. **Install Fail2ban** (Intrusion Prevention):
   - Enabled the community repository in `/etc/apk/repositories`.
   - Installed Fail2ban using:
     ```bash
     apk add fail2ban
     ```

3. **Install Nftables** (Firewall):
   ```bash
   apk add nftables
   ```
   - Installed nftables for firewall configuration.

---

## Setting Up Version Control and Documentation

1. **Install Git** for version control:
   ```bash
   apk add git
   ```

2. **Create a Repository** on GitHub (`m93p-server-setup-and-config`) and initialize a local Git repository:
   ```bash
   git init
   git remote add origin https://github.com/deanOfWalls/m93p-server-setup-and-config.git
   ```

3. **Add README.md and Initial Commit**:
   - Created a `README.md` file to document the setup process.
   - Committed the initial documentation files and pushed to the GitHub repository.

---

## Next Steps

- **Configure Nginx**: Set up server blocks, SSL certificates, and security headers.
- **Configure Fail2ban**: Set up jail configurations for SSH protection.
- **Configure Nftables**: Define firewall rules for SSH, HTTP, HTTPS, and ICMP.
- **Further Security Hardening**: Additional SSH configuration, system updates, and monitoring.

---



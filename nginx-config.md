
# Nginx Configuration for Alpine Linux Server

This document details the configuration of Nginx on the Alpine Linux server, specifically for hosting multiple projects using virtual hosts (server blocks). This setup includes creating server blocks for each project and organizing the structure in a modular way.

## Steps

### 1. Remove Default `localhost` Directory

The default `localhost` directory in `/var/www/` is not needed for our custom configuration. We removed it to keep the directory clean.

```sh
sudo rm -rf /var/www/localhost
```

### 2. Copy Project Files to `/var/www/`

Transferred the project files (`fileserver` and `portfolio`) from the Arch Linux machine to the Alpine server using `scp`.

On the Arch Linux machine:

```sh
scp -r /home/dean/alpine-backups/var/www/fileserver root@192.168.1.157:/var/www/
scp -r /home/dean/alpine-backups/var/www/portfolio root@192.168.1.157:/var/www/
```

### 3. Set Up Nginx Virtual Hosts (Server Blocks)

We created separate configuration files for each project in `/etc/nginx/http.d/`.

#### Portfolio Configuration (`portfolio.conf`)

Created `/etc/nginx/http.d/portfolio.conf` with the following content:

```nginx
server {
    listen 80;
    server_name deanwalls.com www.deanwalls.com;
    root /var/www/portfolio;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    # Security headers (optional)
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
}
```

#### File Server Configuration (`fileserver.conf`)

Created `/etc/nginx/http.d/fileserver.conf` with the following content:

```nginx
server {
    listen 80;
    server_name fileserver.deanwalls.com;
    root /var/www/fileserver;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    # Security headers (optional)
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
}
```

### 4. Test and Reload Nginx

1. **Test Nginx Configuration**: Ensure the configuration is correct by testing it with `nginx -t`.

   ```sh
   sudo rc-service nginx configtest
   ```

2. **Reload Nginx**: Apply the changes by reloading Nginx.

   ```sh
   sudo rc-service nginx reload
   ```

---

## Summary

Nginx has been configured with separate virtual hosts for each project, each with its own server block. We are using `/etc/nginx/http.d/` for these server blocks as per Alpine Linux’s convention, and commands are managed with `rc-service` as per OpenRC’s standards.


# Nginx Configuration for Alpine Linux Server

This document details the configuration of Nginx on the Alpine Linux server, specifically for hosting multiple projects using virtual hosts (server blocks). This setup includes creating server blocks for each project and organizing the structure in a modular way.

## Steps

### 1. Remove Default `localhost` Directory

The default `localhost` directory in `/var/www/` is not needed for our custom configuration. We removed it to keep the directory clean.

```sh
rm -rf /var/www/localhost
```

### 2. Copy Project Files to `/var/www/`

Transferred the project files (`fileserver` and `portfolio`) from the Arch Linux machine to the Alpine server using `scp`.

On the Arch Linux machine:

```sh
scp -r /home/dean/alpine-backups/var/www/fileserver root@192.168.1.157:/var/www/
scp -r /home/dean/alpine-backups/var/www/portfolio root@192.168.1.157:/var/www/
```

### 3. Global Configuration in `nginx.conf`

In `/etc/nginx/nginx.conf`, we set up global configurations, including general settings, hardening measures, and rate limiting. Key sections:

- **Global Security Headers** to enforce security policies across projects.
- **Limit Request Sizes** to prevent abuse through large requests.
- **Connection and Rate Limiting** to control simultaneous connections and request rates per IP.

Example `nginx.conf` setup:

```nginx
http {
    # Basic security headers
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self';";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Limit request sizes
    client_body_buffer_size 10K;
    client_header_buffer_size 1k;
    large_client_header_buffers 2 1k;

    # Connection limits per IP
    limit_conn_zone $binary_remote_addr zone=addr:10m;
    limit_conn addr 10;

    # Rate limiting zone definition
    limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;

    include /etc/nginx/http.d/*.conf;
}
```

### 4. Set Up Nginx Virtual Hosts (Server Blocks)

We created separate configuration files for each project in `/etc/nginx/http.d/`.

#### Portfolio Configuration (`portfolio.conf`)

Created `/etc/nginx/http.d/portfolio.conf` with the following content:

```nginx
server {
    listen 80;
    server_name deanwalls.com www.deanwalls.com;
    root /var/www/portfolio;
    index index.html;

    # Security headers for portfolio
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self';";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Custom 403 error page
    error_page 403 /403.html;
    location = /403.html {
        internal;
    }

    # Restrict access to hidden files
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }

    # Rate limiting
    location / {
        limit_req zone=one burst=10 nodelay;
        try_files $uri $uri/ =404;
    }
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

    # Security headers for file server
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self';";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Custom 403 error page
    error_page 403 /403.html;
    location = /403.html {
        internal;
    }

    # Restrict access to hidden files
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }

    # Rate limiting
    location / {
        limit_req zone=one burst=10 nodelay;
        try_files $uri $uri/ =404;
    }
}
```

### 5. Test and Reload Nginx

1. **Test Nginx Configuration**: Ensure the configuration is correct by testing it with `nginx -t`.

   ```sh
   nginx -t
   ```

2. **Reload Nginx**: Apply the changes by reloading Nginx.

   ```sh
   rc-service nginx reload
   ```

---

## Summary

Nginx has been configured with separate virtual hosts for each project, each with its own server block in `/etc/nginx/http.d/`. Global settings and hardening measures are applied in `nginx.conf`, while project-specific settings, custom 403 pages, and rate limiting are configured individually in each `.conf` file. This setup is modular, secure, and optimized for hosting multiple projects on a single server.

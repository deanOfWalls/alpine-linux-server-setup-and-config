
# SSL Certificate Setup with Certbot

This document outlines the steps to set up SSL certificates on the Alpine Linux server using Certbot and configure Nginx to serve HTTPS for each project.

## Steps

### 1. Install Certbot

To obtain SSL certificates, install Certbot and the Nginx plugin.

```sh
apk update
apk add certbot certbot-nginx
```

### 2. Obtain SSL Certificates for Each Domain

Since Nginx was using port 80, we used Certbot in **standalone mode** to obtain certificates for each domain. This process involves stopping Nginx temporarily to free up port 80.

#### Note: Temporarily Disabling HTTPS Blocks

To avoid issues with Nginx trying to serve HTTPS before the SSL certificates are obtained, it’s necessary to **comment out or temporarily disable** the HTTPS blocks in each server configuration file before starting the Certbot process. After the SSL certificates have been obtained, you can **uncomment or re-enable** the HTTPS blocks.

#### For `deanwalls.com` and `www.deanwalls.com`

1. **Stop Nginx**:
   ```sh
   rc-service nginx stop
   ```

2. **Run Certbot** to obtain certificates:
   ```sh
   certbot certonly --standalone -d deanwalls.com -d www.deanwalls.com
   ```

3. **Start Nginx** again after obtaining the certificates:
   ```sh
   rc-service nginx start
   ```

#### For `fileserver.deanwalls.com`

Repeat the steps above to obtain a certificate for the fileserver subdomain:

1. **Stop Nginx**:
   ```sh
   rc-service nginx stop
   ```

2. **Run Certbot**:
   ```sh
   certbot certonly --standalone -d fileserver.deanwalls.com
   ```

3. **Start Nginx**:
   ```sh
   rc-service nginx start
   ```

### 3. Update Nginx Configuration to Use SSL

Once the certificates are obtained, configure Nginx to use them by re-enabling the HTTPS server blocks in each configuration file (`portfolio.conf` and `fileserver.conf`) and adding the following paths:

- **SSL Certificate**: `/etc/letsencrypt/live/<your-domain>/fullchain.pem`
- **SSL Certificate Key**: `/etc/letsencrypt/live/<your-domain>/privkey.pem`

Example:

```nginx
server {
    listen 443 ssl;
    server_name deanwalls.com www.deanwalls.com;

    ssl_certificate /etc/letsencrypt/live/deanwalls.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/deanwalls.com/privkey.pem;

    # other settings like root, index, location, etc.
}
```

Repeat this for `fileserver.deanwalls.com` in its own configuration file.

### 4. Set Up Auto-Renewal for SSL Certificates

Certbot’s certificates expire every 90 days, so it’s important to set up automatic renewal.

1. **Add a Cron Job for Renewal**:
   - Open the crontab editor:
     ```sh
     crontab -e
     ```
   - Add the following line to check for certificate renewal twice a day:
     ```plaintext
     0 */12 * * * certbot renew --quiet
     ```

2. **Test Renewal**:
   - You can test the renewal process with a dry run to ensure it’s set up correctly:
     ```sh
     certbot renew --dry-run
     ```

   If the dry run is successful, the auto-renewal is correctly configured.

---

## Summary

This setup ensures that the domains `deanwalls.com`, `www.deanwalls.com`, and `fileserver.deanwalls.com` are accessible over HTTPS. Automatic renewal is set up to keep certificates up-to-date, ensuring continuous secure access to your projects.

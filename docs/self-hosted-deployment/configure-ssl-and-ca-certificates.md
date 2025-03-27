---
sidebar_position: 94
---

# Configure SSL and CA Certificates

In today's digital landscape, securing web applications is paramount. One of the fundamental aspects of web security is
implementing SSL/TLS encryption to protect data transmitted between clients and servers. In this guide, we'll delve into
configuring SSL and CA certificates for Docker-based installations, ensuring your web applications are safeguarded
against potential threats.

## Setting up Domain:

1. Begin by configuring your domain name in the Nginx configuration file. Copy the default `nginx.conf` file and replace
   occurrences of `%domainname%` with your actual domain name.
   ```bash
   cp init/nginx.conf nginx.conf
   # Make changes in nginx.conf replacing `%domainname%` with `domain_actual`
   ```
2. Update the `builder_url` in the `dronahq.env` file to reflect your assigned domain.
   ```bash
   nano dronahq.env
   # Update builder_url to https://domain_name
   ```
3. Restart the web server to apply the changes.

   ```bash
   sudo docker compose stop webapp webserver
   sudo docker compose rm webapp webserver
   sudo docker compose up -d webapp webserver
   ```

## Configuring SSL for Docker-Based Installation 

### Configure Self-Signed Certificates:

To configure SSL for your Docker-based installation, follow these steps to generate and implement a self-signed
certificate:

1. Copy the default configuration file `nginx.conf` located in the `init` directory
   to the root directory and make necessary changes to accommodate your domain.

   ```bash
   cp init/nginx.conf nginx.conf
   ```

   Update occurrences of `%domainname%` with your actual domain name in the `nginx.conf` file.

2. Restart the web server to apply the updated configuration.

   ```bash
   sudo docker compose restart webserver
   ```

3. Generate the SSL certificate using Certbot with the provided Docker Compose
   configuration. Replace `<your_email_id>` with your email address and `<your_custom_domain>` with your custom domain.

   ```bash
   sudo docker compose -f certbot-docker-compose.yml run --rm certbot certonly --webroot --webroot-path /var/www/certbot/ --force-renewal --email <your_email_id> -d <your_custom_domain> --agree-tos --non-interactive
   ```

4. Copy the default configuration file `nginx-ssl-default.conf` from the
   `init` directory to the root directory, then replace occurrences of `%domainname%` with your actual domain name.

   ```bash
   cp init/nginx-ssl-default.conf nginx.conf
   ```

   Update all occurrences of `%domainname%` with your real domain in the `nginx.conf` file.

5. Modify the `builder_url` in the `dronahq.env` file to use HTTPS with your domain name.

   ```bash
   nano dronahq.env
   # Replace builder_url with https://domain_name
   ```

6. Restart Services: Stop and remove the web application and web server containers, then bring them back up to apply
   the SSL configuration.
   ```bash
   sudo docker compose stop webapp webserver
   sudo docker compose rm webapp webserver
   sudo docker compose up -d webapp webserver
   ```

### Configuring Custom Certificates:

1. If you have custom SSL certificates, copy them to a designated folder on your machine. Let's denote this folder as XYZ.
2. Update the `docker-compose.yml` file to include the volume mapping for the certificate files. Replace `/xyz/` with the path to your custom certificate folder and ensure it's appropriately mounted to `/certificates/` within the container.
   ```yaml
   services:
     webserver:
       volumes:
         - /path/to/XYZ/:/certificates/:ro
   ```
   Ensure that XYZ can be replaced with the actual folder name where your certificates are stored.
3. Modify the `nginx.conf` file to point to the custom certificate locations.
   ```bash
   cp init/nginx-ssl-default.conf nginx.conf
   # Replace all occurrences of `%domainname%` with your actual domain name
   Replace:
     ssl_certificate /etc/nginx/ssl/live/%domainname%/fullchain.pem;
     ssl_certificate_key /etc/nginx/ssl/live/%domainname%/privkey.pem;
   With:
     ssl_certificate /certificates/fullchain.pem;
     ssl_certificate_key /certificates/privkey.pem;
   ```
4. Restart the web server to apply the custom certificate configuration.
   ```bash
   sudo docker compose stop webapp webserver
   sudo docker compose rm webapp webserver
   sudo docker compose up -d webapp webserver
   ```



<!-- Work in progress... -->

<!-- ## configure SSL for Docker based installation

### Configure self signed certificate

### configure custom certificates

## configure ssl for kubernetes installation on kubernetes cluster

### configure self signed certificate

### configure custom certificates

## configure ssl on load balancer for installation with multi-instance auto scaling

 -->

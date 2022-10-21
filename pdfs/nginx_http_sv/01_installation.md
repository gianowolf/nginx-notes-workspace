# NGINX Basics

## Ubuntu Server 22.04 Installation

```sh
apt-get update
apt install -y curl gnupg2 ca-certificates lsb-release \
    debian-archive-keyring
```

```sh
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
```

```sh
apt-get update
apt-get install -y nginx
nginx
```

## Directories

### /etc/nginx

Default configuration root for the NGINX server. You will find configurration files that instruct NGINX on how to behave.

- /etc/nginx/nginx.conf: This file is the default config entry point used by the NGINX service. Sets up global settings for things like worker processes, tuning, logging, loading, etc.
- /etc/nginx/conf.d/: This directory contains default HTTP server configuration file.
- /var/log/nginx/ directory is the default log location for NGINX. access.log and error.log files. 

## Commands

```sh
nginx -h
nginx -v
nginx -V
nginx -t #tests configration
nginx -s signal # Sends a signal to the NGINX process such stop, quit, reload and reopen
```

- You can alter the default config files and test your changes with the nginx -t command. 
- If your test is successful, reload the configuration using ```nginx -s reload``` command

## Serving Static Content 

Overwrite the default HTTP server configuration locted in /etc/nginx/conf.d/default.conf wit hthe following nginx configuration example:

```conf
server {
    listen 80 default_server;
    server_name www.example.com;

    location / {
        root /usr/share/nginx/html;
        # alias /usr/share/nginx/hatml;
        index index.html index.htm;
    }
}
```

- This config serves static files over HTTP on port 80 from the directory /usr/share/nginx/html.
- The first line in this config defines a NEW SERVER BLOCK. This define a new context for NGINX to listen for.
  - line two instructs NGINX to listen port 80 
  - serveR_name directive defines the hostname 
  - The location block defines a configuration based on the path in the URL.


# 3 HTTP Configuration

- Websites served are also referred as virtual hosts
- http/server/location 
- HTTP directives and variables 

## HTTP Core Module 

- Directives
- Components 
- Variables 

## Structure Blocks 

HTTP Introduces three logical blocks 

- http: 
  - At the root of config file 
  - Define the directives and blocks for all the modules related to HTTP
  - If defined multiple times (no real purpose) the last block will override the previous ones.
- server:
  - Declare a **website** identified by one or more hostnames
  - Can only be used within http block
- location
  - Define settings to be applied to a particular location on a website 
  - Can be used within a server block or another location block

HTTP section encompasses entire web config.

- Contain one or more server blocks 
- Defining domains and sub-domains
- define location blocks for each websites to define additional settings to a particular URI.
- A setting at http block level preserves its value in the potentially incorporated server and location blocks

```yaml
http {
    gzip on;
    server:{
        server_name localhost;
        listen 80;
    }
    location /downloads/ {
        gzip off;
    }
}
```

## Socket and Host Config

- Directives to configure virtual hosts
- Materializes by ```server``` blocks 
  - hostname or IP address
  - port combination
- TCP socket options

### listen

- Context: server
- IP Address
- Port to be used by listening socket 
  - 80 HTTP, 443 HTTPS 
- ```listen [address][:port] [options];```
- Options
  - default_server: default website for any request received at the IP and port
  - ssl: Served over SSL
  - spdy: Support for SPDY protocol if SPDY module is present
  - proxy_protocol: Enables PROXY protocol
  - backlog, rcvbuf, sndbuf, accept_filter, deferred, setfib, bind.
  
```yaml
listen 192.168.1.1:80;
listen 127.0.0.1;
listen 80 default;
listen [:::a8c9:1234]:80;
listen 554 ssl;
```

### server_name

- Context: server
- Assigns one or more hostnames to the server block.
- if no server block matches the desired hosts, nginx selects te first server block that matches the parameters on the listen directive.
- Accepts wildcard and regular expressions
- ```server_name hostname1 [hostname2];

```yaml
server_name www.website.com;
server_name www.website.com website.com;
server_name *.website.com;
server_name .website.com; # website.com + *.website.com
server_name *.website.*;
server_name ~^(www)\.example\.com$; # $1 = www
server_name website.com ""; # Catch all the requests
server_name _ ""; # Catch all the request: _ dummy hostname
```

### server_name_in_redirect

- Context: http, server, location
- ```on or off``` default off
- Internal redirects.
  
  ### server_names_hash_max_size

- Context: http
- default 512
- Hash tables for data collections to spped up processing requests. Max size of the server names hash tables.

### server_name_hash_bucket_size

- context: http
- bucket size for server names hash tables. Change this value onl if nginx tells you to do

### port_in_redirect
- Defines whether or not nginx should append to port number to the redirection.

### tcp_nodelay

- context: http, server, location
- Enables or disables TCP_NODELAY socket option for keep-alive connections only.

### tcp_nopush

- http, server, location
- Enables or disables TCP_CORK socket option.

### sendfile

- if Ena, nginx uses sendfile kernel call to handle file transmission. If disabled, handles file transfer by itself. Default off.

### sendfile_max_chunk

- http, server.
- defines max size of data to be used for each call to sendfile
- default 0.

### reset_timedout_connection

- http, server, location
- When connection times out, its associated information may remain in memory depending  on its state. Enabling this directive will erase all memory associated with the connection after it times out.
- default off

---------------------------------------------

## Paths and Documents 

Directives that configure the documents that should be serverd for eachwebsite, as error pages, index page, etc.

### root

- context: http, server, location, if. Variables accepted
- Defines document root **containing the files that you wish to serve to your visitors**
- sintax: directory path
- default: html
- ```root /home/websitecom/public_html;

### alias

- context: location. Variables+
- assigns different path for nginx to retrieve documents for a specific request.

```yaml
http {
    server {
        server_name localhost;
        root/var/www/website.com/html;
        location /admin/ {
            alias /var/www/locked/;
        }
    }
}
```

1. When a request for http://localhost/ is received, files are served from the /var/www/website.com/html folder. 
2. IF receives a http://localhost/admin request, tha path used to retrieve the files is var/www/locked
3. Do not forget trailing /

### error_page

- context: http, server, location, if. Variables+
- ```error_page code1 [code2...] [=replacement code] [=@block | URI]
- Allows to affect URIs to the HTTP response code
  
```yaml
error_page 404 /not_foud.html;
error_page 500 501 502 503 504 /server_error.html;
error_page 403 http://website.com/;
error_page 404 @notfound; #jump to a named location block
error_page 404 =200 /index.html; #404 error, redirecto to index with 200 ok responde code
```

### if_modified_since

- http, server, location
- HTTP Modified-Since header used by seach engine spiders, such as Google web crawling bots. The robot indicates date and time of the last pass. If the server has not been modified since that time, server simply returns a 304 nod modified response code wit no body.
- accepts values off, exact, before.
- default: exact

### index

- Context: http, server, location. Variables+
- 
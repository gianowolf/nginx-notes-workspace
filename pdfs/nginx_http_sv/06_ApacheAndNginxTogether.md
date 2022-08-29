# Chapter 6: Apache and Nginx Together

## Nginx as Reverse Proxy

Cases to use Nginx as reverse proxy

- Already installed with complex configuration files that can hardly be ported to Nginx
- When sistem opertas a frontend system management such as cPanel.
- When functionality that the project requires is available with apache but not with nginx

In any other case a complete switch to Nginx would be a better choice.

### Reverse Proxy Mechanism

- Nginx as a frontend sever, direct communication with outside world
- Apache running as a backend server

#### Nginx

- Positioned as a frontend web server (reverse proxy)
- Receives all the requests comming from the outside net
  
#### Apache

- Runs as backend server and
- The listening port must be edited to leave port 80 available to Nginx.
- Alternatively, can employ multiple backend servers on different machines and share the load

To communicate and interact each other -> FastCGI.  Nginx acts as a proxy server. Receives HTTP requests from the client and forwards them to the backend server. The mechanism is handled by the proxy module of Nginx.


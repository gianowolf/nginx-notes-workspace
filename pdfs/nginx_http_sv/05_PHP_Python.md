# PHP and Python with Nginx

## FastCGI

### CGI Mechanism

The original purponse of a web server was merely to resond to requets from clients by serving te files located on a storage device.

1. Client sends request: ```GET /index.html HTTP/1.1``` to the web server
2. Web Server process request and sends resopnse ```HTTP/1.0 OK or 404 file not found```

Static websites are being progressively abandoned atthe exprense of dynamic ones that contains scripts which are processed by applications such as PHP and Python among others.

1. Client entity (typically web browser) sends request: ```GET /index.php HTTP/1.1```
2. Web server pre-processes the reequest (URL rewriting, internal redirects)
3. Web server (Nginx, Apache) forwards request using CGI
4. Backend application (Application, Python, NodeJS) processes request 
5. Backed application returns response using CGI
6. Web server post-processes response (gzip compression, character encoding, etc)
7. Web server returns response ```HTTP/1.0 200 OK```
   
When client attmps to visit a dynamic page, web server forwards to the application, processes the script and returns the response to the web server.

To communicate with that ap, CGI protocol was invented in the early 1990s.

## Common Gateway Interface 

- CGI Protocol v1.1 [ RFC3875 ]
- Allows an HTTP server and a CGI script to share responsibility for resonding a cilent requests 
- Server: responsible for managing conection, data transfer, transport, network issues 
- CGI script: handles the app issues, data access and document processing
- CGI protocol describes the data exchange between web server and gateway application.

## Fast Common Gateway Interface

CGI Protocol is inneficient for servers that are subjects to heavy loads.

- PErsistent processes that handle multiple requests
- server and gateway coomunicates using sockets as TCP or POSIX IPC -> Web Server and application can located in different computers
- It can be implemented on any platform with any programming language.
- Not complex to implement.
- 
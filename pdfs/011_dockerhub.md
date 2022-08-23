# Nginx Docker Hub

## About 

- Nginx is an open source reverse proxy server for 
  - HTTP
  - HTTPS
  - STMP
  - POP3
  - IMAP
- Load Balancer
- HTTP Cache
- Web Server 

Adventajes 
- High Performance
- High Concurrency
- Low Memory Usage
  
## Image Usage

### Hosting Static Content 

```sh
docker run --name my-nginx-container -v /host/path/nginx.conf:/etc/nginx/nginx/.conf:ro -d nginx
```

Dockerfile can be used to generate a new image that includes the necessary content

```Dockerfile
FROM nginx
COPY static-html-directory /usr/share/nginx/html
```

Place this file in the same directory as your directory of content, run ```docker build -t some-content-nginx .``` then start the container

```sh
docker run --name some-nginx -d -p 8080:80 some-content-nginx
```

then you can visit ```http://localhost:8080/``` in the browser.


## Complex Config

```sh
docker run --name my-custom-nginx-container -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx
```

to addapt default configuration file

```sh
docker run --name tmp-nginx-container -d nginx
docker cp tmp-nginx-container:/etc/nginx/nginx.conf /host/path/nginx.conf
docker rm -f tmp-nginx-container
```

```Dockerfile
FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
```

To add custom ```CMD``` in Dockerfile be sure to include ```-g daemon off;``` in the CMD order for nginx to stay in the foreground, so that docker can track the preocess properly.

Then build the image with ```docker build -t custom-nginx .``` an run it as

```sh
docker run --name my-custom-nginx-container -d custom-nginx
```

## Environment Variables

- Nginx does not support environment variables inside most configuration blocks
- This image extract environment variables before nginx starts.
  
Using docker-compose: 

```yml
web:
  image: nginx
  volumes:
    - ./templates:/etc/nginx/templates
  ports:
    - "8080:80"
  environment:
    - NGINX_HOST=foobar.com
    - NGINX_PORT=80
```

This function reads template files in /etc/nginx/templates/*.template and outputs the result of executing ```envsubst``` to ```/etc/nginx/conf.d```.

## Nginx Read Only Mode

1. Mount a docker volume to every location where nginx writes information.
2. Default Nginx config requires write-access to /var/cache and /var/run
3. To advanced configuration that requires nginx to write other location, add more volume mounts to those locations.

```sh
docker run -d -p 80:80 --read-only -v $(pwd)/nginx-cache:/var/cache/nginx -v $(pwd)/nginx-pid:/var/run nginx
```

## Debug Mode

```sh
docker run --name my-nginx -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx nginx-debug -g 'daemon off;'
```

```yml
web:
  image: nginx
  volumes:
    - ./nginx.conf:/etc/nginx/nginx.conf:ro
  command: [nginx=debug, '-g', 'daemon-off;']
```
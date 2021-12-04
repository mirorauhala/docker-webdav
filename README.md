# Simple nginx-webdav server

## Clone the repository

```
git clone https://github.com/mirorauhala/docker-webdav.git
```

## Build the image

```
docker build -t mirorauhala/webdav:latest .
```

## Running the container

### docker-cli

--restart=unless-stopped: Iniciar cada vez que iniciemos el servidor

```

WEBDAV_USERNAME=webdav
WEBDAV_PASSWORD=webdav
WEBDAV_PORT=8080

docker run --name webdav \
  --restart=unless-stopped \
  -p $WEBDAV_PORT:8080 \
  -v $HOME/docker/webdav:/media \
  -e USERNAME=$WEBDAV_USERNAME \
  -e PASSWORD=$WEBDAV_PASSWORD \
  -e TZ=Europe/Helsinki \
  -e UDI=1000 \
  -e GID=1000 \
  -d  mirorauhala/webdab
```

### docker-compose with Traefik reverse-proxy

```
version: '2'
services:
  webdav:
    container_name: webdav
    image: mirorauhala/webdav
    ports:
      - 8080:8080
    volumes:
      - $HOME/docker/webdav:/media
    environment:
      - USERNAME=webdav
      - PASSWORD=webdav
      - UID=1000
      - GID=1000
      - TZ=Europe/Helsinki
    networks:
      - web
    labels:
      - traefik.backend=webdav
      - traefik.frontend.rule=Host:webdav.tu_dominio.duckdns.org
      - traefik.docker.network=web
      - traefik.port=80
      - traefik.enable=true
      # Adding in secure headers
      - traefik.http.middlewares.securedheaders.headers.forcestsheader=true
      - traefik.http.middlewares.securedheaders.headers.sslRedirect=true
      - traefik.http.middlewares.securedheaders.headers.STSPreload=true
      - traefik.http.middlewares.securedheaders.headers.ContentTypeNosniff=true
      - traefik.http.middlewares.securedheaders.headers.BrowserXssFilter=true
      - traefik.http.middlewares.securedheaders.headers.STSIncludeSubdomains=true
      - traefik.http.middlewares.securedheaders.headers.stsSeconds=63072000
      - traefik.http.middlewares.securedheaders.headers.frameDeny=true
      - traefik.http.middlewares.securedheaders.headers.browserXssFilter=true
      - traefik.http.middlewares.securedheaders.headers.contentTypeNosniff=true
networks:
  web:
   external: true
```

Introduce el comando...

```
docker-compose up -d
```

## Logs

View the logs.

(TODO: use stdout)

### Access logs

```
docker exec -it webdav cat /var/log/nginx/webdav_access.log
```

### Error logs

```
docker exec -it webdav /var/log/nginx/webdav_error.log
```

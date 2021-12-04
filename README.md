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
  -d  ghcr.io/mirorauhala/webdab
```

## Logs

View the logs.

### Access logs

```
docker exec -it webdav cat /var/log/nginx/webdav_access.log
```

### Error logs

```
docker exec -it webdav /var/log/nginx/webdav_error.log
```

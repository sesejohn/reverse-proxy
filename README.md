# Reverse Proxy
<div align="center">
  <img src="https://olex.biz/2019/09/hosting-with-docker-nginx-reverse-proxy-letsencrypt/proxy-diagram-large.png" />
</div>
<div align="center">
   Credits to @olexs
</div>

## nginx-proxy
Sets up a container running nginx and docker-gen ([previously created separately](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/)). docker-gen generates reverse proxy configs for nginx and reloads nginx when containers are started and stopped.

## acme-companion (Let's Encrypt SSL Companion)
acme-companion is a lightweight companion container for nginx-proxy.

It handles the automated creation, renewal and use of SSL certificates for proxied Docker containers through the ACME protocol.

## How to use (after cloning this repo)
### 1. Create the docker network
```
docker network create reverse-proxy
```
### 2. Run docker compose command to create nginx-proxy and acme-companion containers
```
docker-compose up -d
```
### 3. (Optional) To check logs, run docker exec command
- nginx-proxy
  ```
  docker logs -f nginx-proxy
  ```
- acme-companion
  ```
  docker logs -f acme-companion
  ```

## Expose Port (nginx-proxy)
- 80
- 443
- IPV6 support ready


## Testing
### 1. On your website directory, create docker.compose.yml
```yaml
services:
  test-website:
    image: nginx:latest
    container_name: test-website
    restart: always
    environment:
      - LETSENCRYPT_HOST=domain.com # @TODO: your public DNS name
      - VIRTUAL_HOST=domain.com  # @TODO: your public DNS name, the same domain as above
      - VIRTUAL_PORT=80 # @TODO: port used
    networks:
      - reverse-proxy # the same network of nginx-proxy and acme-companion

networks:
  reverse-proxy:
    external: true
```
### 2. Run docker-compose to create test-website container
```
docker-compose up -d
```
### 3. Wait up till 5 minutes, then visit your domain
The created test-website should automatically have SSL certificate and it's own nginx server configurations

### References
- https://hub.docker.com/r/nginxproxy/nginx-proxy
- https://hub.docker.com/r/nginxproxy/acme-companion
- http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/
- https://github.com/johnsese/reverse-proxy-old (My old reverse-proxy setup)
- https://letsencrypt.org/docs/

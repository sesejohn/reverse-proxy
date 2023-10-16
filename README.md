# Reverse Proxy

## nginx-proxy
Sets up a container running nginx and docker-gen ([previously created separately](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/)). docker-gen generates reverse proxy configs for nginx and reloads nginx when containers are started and stopped.

## acme-companion
acme-companion is a lightweight companion container for nginx-proxy.

It handles the automated creation, renewal and use of SSL certificates for proxied Docker containers through the ACME protocol.


## Testing
### 1. Create docker.compose.yml
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

## Expose Port (nginx-proxy)
- 80
- 443
- IPV6 support ready

### References
- https://hub.docker.com/r/nginxproxy/nginx-proxy
- https://hub.docker.com/r/nginxproxy/acme-companion
- http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/
- https://github.com/johnsese/reverse-proxy-old (My old reverse-proxy setup)
- https://letsencrypt.org/docs/
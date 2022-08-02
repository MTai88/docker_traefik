# Basic `traefik` container setup

### This setup allows you to run `traefik` container for run projects that using the same port at the same time

### Setup
- create external network `traefik-net`
```
docker network create traefik-net
```
- Example of using traefik in project1:
```
version: "3"

services:
  nginx:
    image: nginx:latest
    volumes:
    - ./app/index.html:/app/index.html
    - ./default.conf:/etc/nginx/conf.d/default.conf
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nginx-dev1.rule=Host(`dev1.domain.ru`)"
      - "traefik.http.services.nginx-dev1.loadbalancer.server.port=80"
      - "traefik.docker.network=traefik-net"
    networks:
    - default
    - dev1

  httpd:
    image: httpd:latest
    volumes:
    - ./app/index.html:/usr/local/apache2/htdocs/index.html
    networks:
      - dev1

networks:
  default:
    external: true
    name: traefik-net
  dev1:
    internal: true
```
- Example of using traefik in project2:
```
version: "3"

services:
  nginx:
    image: nginx:latest
    volumes:
    - ./app/index.html:/app/index.html
    - ./default.conf:/etc/nginx/conf.d/default.conf
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nginx-dev2.rule=Host(`dev2.domain.ru`)"
      - "traefik.http.services.nginx-dev2.loadbalancer.server.port=80"
      - "traefik.docker.network=traefik-net"
    networks:
    - default
    - dev2

  httpd:
    image: httpd:latest
    volumes:
    - ./app/index.html:/usr/local/apache2/htdocs/index.html
    networks:
      - dev2

networks:
  default:
    external: true
    name: traefik-net
  dev2:
    internal: true
```

### Running container
`docker-compose up -d`

# swarm02

# ref
https://github.com/docker/awesome-compose/blob/master/wordpress-mysql/compose.yaml


[_docker-compose.yaml_](docker-compose.yaml)
```
version: '3'
services:
  db:
    image: mariadb:10.6.4-focal
    command: '--default-authentication-plugin=mysql_native_password'
    expose:
      - 3306
      - 33060
    volumes:
      - db_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=somewordpress
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - MYSQL_PASSWORD=wordpress
    networks:
      - backend

  wordpress:
    image: wordpress:latest
    environment:
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_USER=wordpress
      - WORDPRESS_DB_PASSWORD=wordpress
      - WORDPRESS_DB_NAME=wordpress
    networks:
      - backend
      - webproxy
    depends_on:
      - db
    deploy:
        replicas: 1
        labels:
          - traefik.docker.network=webproxy
          - traefik.enable=true
          - traefik.constraint-label=webproxy
          - traefik.http.routers.${APPNAME}-https.entrypoints=websecure
          - traefik.http.routers.${APPNAME}-https.rule=Host("${APPNAME}.xops.ipv9.me")
          - traefik.http.routers.${APPNAME}-https.tls.certresolver=default
          - traefik.http.services.${APPNAME}.loadbalancer.server.port=80
          - traefik.http.routers.${APPNAME}-https.tls=true
        resources:
          reservations:
            cpus: '0.1'
            memory: 10M
          limits:
            cpus: '0.4'
            memory: 250M


volumes:
  db_data:

networks:
  backend:
    driver: overlay
  webproxy:
    external: true  
```

# step 

1.clone wordpress-mysql project from https://github.com/docker/awesome-compose/blob/master/wordpress-mysql/compose.yaml\
2.add traefic config to docker-compose
```
...
    networks:
      - backend
      - webproxy
    depends_on:
      - db
    deploy:
        replicas: 1
        labels:
          - traefik.docker.network=webproxy
          - traefik.enable=true
          - traefik.constraint-label=webproxy
          - traefik.http.routers.${APPNAME}-https.entrypoints=websecure
          - traefik.http.routers.${APPNAME}-https.rule=Host("${APPNAME}.xops.ipv9.me")
          - traefik.http.routers.${APPNAME}-https.tls.certresolver=default
          - traefik.http.services.${APPNAME}.loadbalancer.server.port=80
          - traefik.http.routers.${APPNAME}-https.tls=true
        resources:
          reservations:
            cpus: '0.1'
            memory: 10M
          limits:
            cpus: '0.4'
            memory: 250M
...

```
3.deploy

# output

<img src="./output.jpg">

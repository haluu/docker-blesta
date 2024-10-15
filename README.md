# Docker-Blesta
A docker image for ⁠[Blesta](https://www.blesta.com) base on Nginx, PHP8.2, ioncube on Alpine Linux.

# How To Use
Example with docker compose and mariadb.

Create network
```
docker network create blesta
docker network create proxy
```

Run docker compose file
```
docker compose up -d
```

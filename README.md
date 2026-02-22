# Docker-Blesta
Unofficial Docker image for ⁠[Blesta](https://www.blesta.com) built on Alpine Linux with Nginx, PHP 8.2 (FPM) and ionCube Loader.

Features
- Nginx + PHP-FPM (PHP 8.2)
- ionCube Loader installed
- Lightweight Alpine base
- Minimal image that contains the runtime Blesta requires

Requirements
- Docker and Docker Compose
- A Blesta installation fileset (this image provides the runtime only)
- A valid Blesta license for production use (see License Requirement below)

Quick Start (docker-compose)

Create a `docker-compose.yml` next to this repository and adapt the values below:

```yaml
services:
	blesta:
		image: haluu/docker-blesta:latest
		ports:
			- "8080:80"
		depends_on:
			- db
        networks:
            - proxy
            - blesta
		volumes:
			- ./blesta_data:/var/www/html

	db:
		image: mariadb:8
		environment:
			- MYSQL_ROOT_PASSWORD=example
			- MYSQL_DATABASE=blesta
			- MYSQL_USER=blesta
			- MYSQL_PASSWORD=secret
        networks:
            - blesta
		volumes:
            - ./db_data:/var/lib/mysql
networks:
  proxy:
    external: true
  blesta:
    external: true
volumes:
	db_data:
	blesta_data:
```

Create network
```
docker network create blesta
docker network create proxy
```

Start services:

```bash
docker compose up -d
```

Notes
- Do not use the `latest` tag in production; prefer pinned tags and take backups.
- This image provides the runtime environment only; you must supply a Blesta install and database.

License Requirement
Blesta requires a valid license to operate. When running Blesta in Docker, a Blesta license may be required. Ensure you have the appropriate license before deploying this image in production.

Contributing
If you find issues or want to improve the image, open a PR or issue on the repository hosting this project.

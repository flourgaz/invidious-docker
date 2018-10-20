# Invidious Docker

This repo contains the Dockerfile to build a production image of Invidious - the alternative youtube frontend.

Image is automatically built on Docker Hub under the name `flourgaz/invidious`

To spin up your own instance using docker-compose:

1. create a `docker-compose.yml` file with this content:
```
version: '3'
services:
  postgres:
    image: postgres:10
    restart: unless-stopped
    volumes:
      - postgresdata:/var/lib/postgresql/data
  invidious:
    image: flourgaz/invidious
    restart: unless-stopped
    volumes:
      - ./config.yml:/invidious/config/config.yml
    ports:
      - "3000:3000"
    depends_on:
      - postgres

volumes:
  postgresdata:
    external:
      name: invidious_postgresdata
```

2. create your own invidious `config.yml` (you can use the one provided in invidious github, just use `postgres` as the hostname for the database container)

3. before the first run you have to setup the database structure and save it in a volume `invidious_postgresdata` (you can skip this step if you already have the volume)
   - create empty volume

     `docker volume create invidious_postgresdata`
   - start an empty postgres container, mounting the new volume and invidious source directory

     `docker run --name invidious_db_setup -d -v invidious_postgresdata:/var/lib/postgresql/data -v /path/to/invidious/repo:/invidious postgres:10`
   - jump into the container

     `docker exec -it -u postgres invidious_db_setup bash`
   - inside the container, run setup

     `cd /invidious; ./setup.sh`
   - exit the container and remove it

     `docker rm -f invidious_db_setup`

   volume is now ready to use

4. `docker-compose up`
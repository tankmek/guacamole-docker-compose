# guacamole-docker-compose
Build Apache Guacamole using docker compose. Utilizes Docker secrets and a persistent database volume.

## Prerequisites

`docker ce`  
`docker-compose >= 1.23.0`

## Usage

```
git https://github.com/c0demech/guacamole-docker-compose.git
cd docker-compose-guacamole
sudo docker-compose -p guacamole up -d
```

You can access the application at `http://docker-host:8080/guacamole/`. Unless you have an nginx or apache reverse proxy configured. The default username and password are both `guacadmin` remember to _change_ this and create a separate user.

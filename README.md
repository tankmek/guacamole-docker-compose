[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Ftankmek%2Fguacamole-docker-compose&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%233A57E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

# guacamole-docker-compose
Build Apache Guacamole using MariaDB and Nginx with docker compose. Utilizes Docker secrets and a persistent database volume.

I have an upstream WAF and Reverse Proxy that uses LE Certificates. That proxy connects to this instance and some other services I self host. This project was created so I could quickly stand up guacamole in my environment. It can work for you as well if u want to leverage self signed certificates. You are welcome to incorporate a LE version that auto updates.

## Tested On
`CentOS 7`  
`CentOS 8`

## Prerequisites

`docker ce`  
`docker-compose >= 1.23.0`  
`git`

```
guacamole
├── docker-compose.yml
├── .env
├── guacamole-user
├── mysql-root
└── nginx
    ├── nginx.conf
    ├── sites
    │   └── gproxy
    └── ssl
        ├── dhparam.pem
        ├── nginx-priv.key
        ├── nginx-pub.crt
        └── nginx-ssl.conf

```
| File | Description |
| --- | --- |
| .env | Docker compose will pickup environment vars from this file |
| guacamole-user | Put your guacamole user password here. |
| mysql-root | Put your MySQL root password here. |
| nginx.conf |nginx site wide configuration. |
| gproxy | vhost configuration file for nginx. |
| dhparam.pem | Diffie-Hellman parameters for nginx. (needs to be generated) |
| nginx-pub.crt | TLS public certificate for nginx. (needs to be generated) |
| nginx-prv.key | TLS private key for nginx. (needs to be generated) |
| nginx-ssl.conf | TLS settings for nginx |


## Usage

```
sudo systemctl enable docker
sudo systemctl start docker 
sudo docker volume create --name=db
git clone https://github.com/tankmek/guacamole-docker-compose.git
cd guacamole-docker-compose/guacamole
openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout ssl/nginx-priv.key -out ssl/nginx-pub.crt
openssl dhparam -out ssl/dhparam.pem 4096
sudo docker run --rm guacamole/guacamole:1.5.0 /opt/guacamole/bin/initdb.sh --mysql > initdb.sql
sudo setenforce 0
sudo docker-compose -p guacamole up -d
sudo docker cp initdb.sql db:/tmp
sudo docker exec -it db bash
mysql -u root -p
use mysql;
drop user guacamole_user;
create user guacamole_user@'%' identified by 'GuacUserPasswd';
GRANT SELECT,INSERT,UPDATE,DELETE ON guacamole_db.* TO 'guacamole_user'@'%'; 
flush privileges;
use guacamole_db;
source /tmp/initdb.sql
exit
exit
```

You can access the application at `https://docker-host`. The default username and password are both `guacadmin` remember to _change_ this and create a separate user.

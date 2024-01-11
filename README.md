# CC-docker-cmds

> [!NOTE]
> COMMANDS BELOW ARE NOT FOR EVERYONE UNTIL COMMON COMMANDS.
```
 pacman -Ss docker
```
OR
```
 pacman -U docker-desktop-4.25.0-x86_64.pkg.tar.zst
```
## Starting and stoping the service.
```
 systemctl status docker.service
 systemctl start docker.service
```
```
CONT=nginx-html;sudo docker stop $CONT; sudo docker remove $CONT
```
 # COMMON COMMANDS.
 ```
 docker ps
 docker ps -a
 docker ps -l
 docker stop 141894f52c0e

 docker exec -it 7b10b1e9e1ef bash
 docker compose up -d

docker logs -f container-name

 docker start container-name
 docker stop container-name
 docker remove container-name

 docker network ls
```

# HTTPD
```
 docker pull httpd
 docker run -d -p 9090:80 --name=http-container httpd

 docker stop http-container
 docker start http-container
 docker remove http-container
 ```

# NGINX
```
 docker pull nginx
 docker run -d -p 8090:80 --name thenginx nginx
```
### Mapping nginx's HTML directory to host's directory for easy development.

```
docker run -d -p 8080:80 -v /home/rapidswords/docker/docker-volumes/nginx/html:/usr/share/nginx/html --name nginx-html nginx
```

For Windows:
```
docker run -d -p 8080:80 -v D:\docker\nginx:/usr/share/nginx/html --name nginx-html nginx
```

# qBitTorrent

We are going to use compose file for this. Copy and paste this in docker-compose.yaml file.
Change <your path to a directory> to your folder path.

```
---
version: "2.1"
services:
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - WEBUI_PORT=9091
    volumes:
      - <your path to a directory>/docker/qbittorrent-downloads:/downloads
    ports:
      - 9091:9091
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
```

Now run this command in the same folder of this file or pass full path of the file.

```
docker compose up -d
docker logs -f <container-name>
```
This log command will display temporary password for qbittorent's webUI. Use this password to login @ localhost:9091

 # BRIDGE NETWORK NGINX
 
 ## Creating new network
```
 docker network create --driver bridge dryaqua
 docker network inspect dryaqua
```

 ## Spinning 3 new nginx containers 

```
 docker run -d -p 8091:80 --name nginx-one nginx
 docker run -d -p 8092:80 --name nginx-two nginx
 docker run -d -p 8093:80 --name nginx-three nginx
 ```
### IDs of containers for reference:

| Container ID  | Port  | Name          |
|-------------- | ----- | ------------- |
| bdafca915166  | 8091  |  nginx-one    |
| 72f354927f94  | 8092  | nginx-two     |
| b95188d74cf6  | 8093  |  nginx-three  |

 ## Inspect to see ipaddress of container
```
docker inspect b95188d74cf6
```
 ## Connect containers to network
 ```
 docker network connect dryaqua bdafca915166
 docker network connect dryaqua 72f354927f94
```
 ## Execute commands from powershell or terminal (Windows 11 only) for better execution
```
 docker exec -it b95188d74cf6 bash
```
 ## Creating another network and connecting to container.

```
docker network create --driver bridge gigawork
```
```
docker network connect gigawork b95188d74cf6
docker inspect b95188d74cf6
```
 ## Disconnecting from default network of docker.

 ```
docker network disconnect bridge b95188d74cf6
docker network disconnect bridge 72f354927f94
docker network disconnect bridge bdafca915166
```

 # MYSQL 
```bash
 docker pull mysql
```
```
 docker stop b95188d74cf6 72f354927f94 bdafca915166
 docker run -d --name thesql -e MYSQL_ROOT_PASSWORD=123123 -p 3306:3306 -v ~/docker/docker-volumes/mysql:/var/lib/mysql mysql
```

Now open MySQL Workbench and try to login via making a new connection.

> [!NOTE]
> If error of access denied is poping up, remove volume part from command i.e. **-v ~/docker/docker-volumes/mysql:/var/lib/mysql**.

> [!NOTE]
> If port 3306 is being used by some process, follow below.

1. Open Powershell as admin
2. Write
   ```
   netstat -ano
   ```
3. Search for 3306 and note which PID is using that port.
4. Kill that process
   ```
   taskkill /F /PID 5820
   ```

# Random Task

> [!WARNING]
> DO NOT COPY.

```
docker network create --driver=bridge --subnet=172.20.0.0/16 --ip-range=172.20.0.0/24 --gateway=172.20.0.1 giganet
docker run -d -p 27017:27017 --name=themongodb mongo
docker network connect giganet themongodb
docker run -d --name=flaskapp -p 80:5000 sarimbinwaseem/flaskapp:v0.2.2
docker network connect giganet flaskapp
```

Then go to localhost/home

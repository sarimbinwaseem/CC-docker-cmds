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

# Docker cheatsheet

## Docker install shell 
```shell
echo "Uninstall old versions"
sudo apt-get remove docker docker-engine docker.io containerd runc

echo "Update the apt package index and install packages to allow apt to use a repository over HTTPS"
sudo apt-get update
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

echo "Add Docker’s official GPG key"
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

echo "Verify that you now have the key with the fingerprint"
sudo apt-key fingerprint 0EBFCD88

echo "Set up the stable repository"
#You can also use nightly or test version
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"

echo "Install Docekr Engine"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

echo "Install Docker Compose on Linux systems"
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
```

## Docker isolate 
ref:https://dreamlab.net/en/blog/post/user-namespace-remapping-an-advanced-feature-to-protect-your-docker-environments/
- edit /etc/docker/daemon.json 
`vim /etc/docker/daemon`
```shell
{
    "userns-remap": "default",
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "50m",
        "max-file": "5"
    }
}
```
- edit /etc/{subuid, subgid}
`vim /etc/subuid,gid`

```shell
dockremap:1000000:65536
```
- restart docker daemon
`sudo /etc/init.d/docker restart`


## Docker ps 
Docker ps --format "table {{.placeholder}}"
```shell
Placeholder	Description
.ID	Container ID
.Image	Image ID
.Command	Quoted command
.CreatedAt	Time when the container was created.
.RunningFor	Elapsed time since the container was started.
.Ports	Exposed ports.
.Status	Container status.
.Size	Container disk size.
.Names	Container names.
.Labels	All labels assigned to the container.
.Label	Value of a specific label for this container. For example ‘’
.Mounts	Names of the volumes mounted in this container.
.Networks	Names of the networks attached to this container.
```
## docker logs
ref: https://docs.docker.com/engine/reference/commandline/logs/


## Docker build 
```shell
docker build -t pixnet-rabbitmq:2020100809 -f Dockerfile . --no-cache

docker build -t pixnet-rabbitmq:`date "+%Y%m%d%H%M" -f Docekrfile . --no-cache

```

## Docker run image 
```shell
docker run -it -v `docker_path:host_path` --name `name` `image:tag` 

ex:
docker run -it -v /root/pixencoder-data:/root/pixencoder-data --name pixencoder pixnet-encoder:202011161517 bash
```

## Manage Docker as a non-root user
```shell
```

## Docker rm containers
- List all the containers
```shell
docker ps -a --no-trunc
```
- Delete containers
```shell
docker rm `container_id`
```

## Docker images
- List images
```shell
docker images
```
- Delete images 
```shell
docker rmi image
```

## Docker run 
```shell
docker run -d --hostname rabbitmq --name rabbitmq -p 5671:5671 -p 5672:5672 -p 15672:15672 -p 15692:15692 docker-registry.pixnet.systems:5000/pixnet/rabbitmq:latest
```

## Docker restart container
docker exec -it prometheus kill -HUP 1 ; docker logs --tail 10 prometheus

## Docker tag
```shell
docker tag pixnet-rabbitmq:2020100809 pixnet-rabbitmq:latest
docker tag pixnet-rabbitmq:2020100809 docker-registry.pixnet.systems:5000/pixnet/rabbitmq:2020100809
docker tag pixnet-phpmyadmin:latest docker-registry.pixnet.systems:5000/pixnet/rabbitmq:latest
```
## Docker push
```shell
docker push docker-registry.pixnet.systems:5000/pixnet/rabbitmq:2020100809
docker push docker-registry.pixnet.systems:5000/pixnet/rabbitmq:latest
```

## Get docker registry 
ref: https://docs.docker.com/registry/spec/api/#introduction
- Get all registry
```shell
curl -X GET https://docker-registry.pixnet.systems:5000/v2/_catalog
```
- Get certain image tags
```shell
curl -X GET https://docker-registry.pixnet.systems:5000/v2/pixnet/rabbitmq/tags/list
```

## NFS volumns
```shell
services:
  xxx:
    volumns:
    - nfs-video:/nfs/video
    - net-video:/net/video_vol1
volumes:
  nfs-video:
    driver_opts:
      type: "nfs"
      o: "nfsvers=4,addr=10.4.1.6:/vol/home/symlinks/videolink,rw"
      device: ":/nfs/video"
  net-video:
    driver_opts:
      type: "nfs"
      o: "nfsvers=4,addr=10.1.4.253:/vol/video_vol1,rw"
      device: ":/net/video_vol1"
```

## CIFS
ref: https://docs.docker.com/storage/volumes/
- docker create volume
```shell
docker volume create --driver local -opt type=cifs \
  --opt device=//tenmaxsgwarehouse.file.core.windows.net:airflow-dag-stage \
  --opt o=addr="tenmaxsgwarehouse.file.core.windows.net,username="username",password="password",file_mode=0777,dir_mode=0777"
  --name volumename
```

- docker run with volume
```shell
docker run --rm --name airflow-dag-prod -v airflow-dag-prod:/tmp debian:stable
```

- docker run with CIFS
```shell
docker run --rm --name airflow-dag-prod \
  --mount 'src=airflow-dag-prod,target=/app,volume-driver=local,volume-opt=type=cifs,volume-opt=device=//tenmaxsgwarehouse.file.core.windows.net/airflow-dag-prod,"volume-opt=o=addr=tenmaxsgwarehouse.file.core.windows.net,username="username",password="password",file_mode=0777,dir_mode=0777"' \
  debian:stable
```


## error log 
The official httpd driver changes the httpd application’s configuration to write its normal output directly to /proc/self/fd/1 (which is STDOUT) and its errors to /proc/self/fd/2 (which is STDERR). See the Dockerfile.

## add user to docker group
```shell
sudo gpasswd -a $USER docker
sudo usermod -aG docker $USER
```

## Purge container log
ref: https://www.axllent.org/docs/clear-docker-log/
- method 1
```shell
LOG=$(docker inspect -f '{{.LogPath}}' "$CONTAINER" 2> /dev/null)
truncate -s 0 "$LOG"

ex:
LOG=$(sudo docker inspect -f '{{.LogPath}}' rabbit2> /dev/null)
sudo truncate -s 0 "$LOG"
```
- method 2
```shell
echo "" > $(docker inspect --format='{{.LogPath}}'  <container_name_or_id>)
```

## Restart EXITED DOCKER
```shell
docker start $(docker ps -a -q --filter "status=exited")
```

## Prune unused Docker objects
ref:https://docs.docker.com/config/pruning/
```shell
docker image prune -a
docker volume prune -f
```
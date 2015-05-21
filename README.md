# All About Docker - *Docker docs*

## Build Docker to file & load Docker from file

docker save image

```
docker save imageid > image_name
```

docker load image

```
docker load < image_name
```

## CLI
https://docs.docker.com/reference/commandline/cli/

### With repo
Pull all `centos` image on public repo

	docker pull centos
Pull image with `tag/version`

	docker pull centos:latest
### List all local images
Without hidden images (hides intermediate images)

	docker images
With hidden images

	docker images -a
Tree List local images

	sudo docker images --tree
### Docker container 
#### List
view current running container (no exited & stoped)

	docker ps
view all container (existed & stoped included)
	
	docker ps -a
#### Stop | Rename | Remove
-
**Stop** a container

	docker stop <container_id|container_name>
**Rename** a container

	docker tag <image_id|image>

Example

	docker tag server:latest myname/server:latest
	docker tag d583c3ac45fd myname/server:latest
	
**Remove** a container

	docker rm
**Remove** a tag

	docker rmi	
**Remove** a image

	docker rmi
### Persistent | DB | File | Log
-
#### Mounting the file volume from other containers

One way to persist the database data is to store database files in another container.
To do so, first create a container that holds database files:

    docker run -d -v /var/lib/mysql --name db_vol -p 22:22 ubuntu:trusty

You can specify any name of the container by using `--name` option, which will be used in next step.

After this you can start your MariaDB image using volumes in the container created above (put the name of container in `--volumes-from`)
	
	docker run -d --volumes-from db_vol -p 3306:3306 mariadb
#### Mounting the file volume from host file
	sudo docker run -v /doesnt/exist:/foo -w /foo -i -t ubuntu bash

### RUN Docker
Run with privilegde

	run - --privileged
env:	
		run -e "golang_host=/golang/"
run

		run -ti <image> /bin/bash
		
run with name

		run --name "my_name"
non-root

		run -u="": Username or UID
		
working dir

	-w="": Working directory inside the container
network (brdg)

	--dns=[]         : Set custom dns servers for the container
	--net="bridge"   : Set the Network mode for the container
	'bridge': creates a new network stack for the container on the docker bridge
	'none': no networking for this container
	'container:<name|id>': reuses another container network stack
	'host': use the host network stack inside the container
	--add-host=""    : Add a line to /etc/hosts (host:IP)
	--mac-address="" : Sets the container's Ethernet device's MAC address
with host

	/docker run -ti --add-host db-static:86.75.30.9 ubuntu cat /etc/hosts
	172.17.0.22     09d03f76bf2c
hardward

	-m="": Memory limit (format: <number><optional unit>, where unit = b, k, m or g)
	-c=0 : CPU shares (relative weight)

### ---plus---
increase **shm size**

	run as --privileged
	
	vi /etc/fstab
```bash	
tmpfs      /dev/shm      tmpfs   defaults,size=512m   0   0
tmpfs      /dev/shm      tmpfs   defaults,size=2g   0   0
mount -o remount /dev/shm
```
	

## Dockerfiles - Working with docker images
https://docs.docker.com/reference/builder/

### File format
```docco
FROM ubuntu:trusty
MAINTAINER DU MINH TAM <duminhtam@gmail.com>
```

### VOLUME
Add VOLUMEs to allow persistent of config, databases & files

	VOLUME  ["/etc/mysql", "/var/lib/mysql"]
### Container environtment
	ENV MY_ENV_VAR my_env_val
### Expose a port for RUN NETWORK NAT
	EXPOSE 3306
### Copy a file from host folder to *your image*
#### ADD
Add command can download file from URL

	ADD http://mysite.com/create_mariadb_admin_user.sh /create_mariadb_admin_user.sh
#### COPY
	COPY create_mariadb_admin_user.sh /create_mariadb_admin_user.sh
### RUN
Replace file content from image

	RUN sed -i -r 's/bind-address.*$/bind-address = 0.0.0.0/' /etc/mysql/my.cnf
CHMOD a file

	RUN chmod 775 /*.sh
	

### CMD
Command run when container create

There can only be **one** CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.

Example:

	CMD ["/usr/bin/wc","--help"]
Run your bash file with args

	CMD ["run.sh","-h my_host"]

### ENTRYPOINT
https://docs.docker.com/reference/builder/#entrypoint

An ENTRYPOINT allows you to configure a container that will run as an executable.

For example, the following will start nginx with its default content, listening on port 80:	

	docker run -i -t --rm -p 80:80 nginx
Limitation: This will pass -d to your entrypoint

	docker run <image> -d

Your can overwrite your ENTRYPOINT with

	docker run --entrypoint


	
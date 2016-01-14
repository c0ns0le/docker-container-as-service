# docker-container-as-service
Skeleton templates to handle Docker containers as services (init.d, upstart, supervidord) in a Linux System
follow me on [@paulinopadial](http://twitter.com/paulinopadial)
## Dependencies
You need docker [installed](https://docs.docker.com/engine/installation/) on your box

## Install & Config: initd script
Choice a name for the container, for the installation we will use "nginxr"
```sh
$ git clone git@github.com:ppadial/docker-container-as-service.git
$ mv docker-container-as-service/CONTAINER_NAME.initd /etc/init.d/nginx
$ chmod +x /etc/init.d/demo-container
```
Edit the script and change the following lines for your desired information
* "Provides:     CONTAINER_NAME" change CONTAINER_NAME by nginx
* Short-Description and Description are Optional
* DESC="Skeleton Container" change Skeleton Container by a description for your service
* NAME=CONTAINER_NAME change CONTAINER_NAME by nginx

That's all

## Run your container as a service using initd script
```sh
$ service nginx start
```

## Configure your containers
Your first step to have a container in your box is download an image from a docker registry, then create a container with a configuration and then start it.

The script will check when start if the container has been created in the box, in case not, it will download (if needed) and create him. You can also specify special arguments when creates the container (basically arguments for docker run command).
To specify paramaters used in the container creation just copy the docker-container-as-service/service-config to /etc/default
```sh
$ cp docker-container-as-service/service-config /etc/default/nginx
```
And add the parameters for docker run command that will be executed in the following manner
```sh
$  ${DOCKER} run -d --name ${NAME} ${CONTAINER_ARGS} -t ${CONTAINER_IMAGE_NAME}
```
**CONTAINER_ARGS** will be replaced by your arguments specified in this file, it means, that you can specify volumes, port-mapping, links, etc. take a look to the [docker run command reference](https://docs.docker.com/engine/reference/run/) to view all the possibilities.

An example of /etc/default/nginx file:
```sh
#!/bin/bash

# Docker Container as a Service: Container configuration file
# Container Image is used to specify the URL or full name for the IMAGE
#  as an example, your demo-container could be a container from nginx docker image.
# If you use private docker registry (not docker.io) then use USER/name like ppadial/demo-container
# If you leave it blank, the name specified in the initd file as NAME var will be used. It will work for
# all the docker.io public images.
CONTAINER_IMAGE='nginx:1.9.9'
# When the script downloads an image and creates a container uses this args to specify additional configuration
# like Port-Mapping, Volume-Mapping, Links, etc. 
# If you leave it blank, default run command will be ran
CONTAINER_ARGS='-p 8888:80 -p 8889:443'

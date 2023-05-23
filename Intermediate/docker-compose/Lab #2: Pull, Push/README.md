# Lab #2: Pull, Push

## Introduction

### Pull

The docker-compose `pull` command pull down the images which is specified under each service of docker-compose file from the docker hub.

syntax:

-  Pull all images in docker-compose: `docker-compose pull`
-  Pull specific image in docker-compose: `docker-compose pull <service_name>`

### Push

#### docker-compose

The docker-compose `push` command help you tou push the service images to Docker Hub or your own private Docker registry.
syntax:

-  Push all services: `docker-compose push`
-  Push specific service: `docker-compose push <service_name>`

> **Note:** you must name your images with your docker hub username before pushing, this is syntax: `<username>/<image name>:<version>`

#### docker

If you want using docker to push your command, you must 3 step bellow:

-  Tagging image: you must tag your images with docker hub username before pushing:  
    `docker tag <image_name> <docker_hub_username>/<image_name>:<tag_name>`
-  Login to docker hub: `docker login`
-  Pushing: `docker push <docker_hub_username>/<image_name>:<tag_name>`

## Instructions

### Pull

Pull the image docker-compose file and view the compose file.

```sh
$ docker-compose pull
```

Check pulled image

```sh
$ docker image ls nginx
```

### Push

Build the image:

```sh
$ docker-compose build
```

Check the image:

```sh
$ docker image ls quanblue/hello

REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
quanblue/hello        v1        d03e0b547a0e   11 seconds ago   7.33MB
```

Login to docker hub

```sh
docker login
```

Push the helloMessage service

```sh
$ docker-compose push helloMessage
```

---

> **Note:** See detail at
>
> -  [lab5_pull_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/pull_command.html)
> -  [lab6_push_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/push_command.html)

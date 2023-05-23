# Lab #1: Config, Build, Up, Run, Scale, Exec, Images, ps, Log, Port

## Introduction

### Config

The docker-compose `config` Validate the docker-compose file and view the compose file.  
syntax: `docker-compose config`

### Build

The docker-compose `build` used to create a new image using the instructions in the Dockerfile.  
syntax: `docker-compose build`

### Up

The docker-compose `up` command help you to bring up a multi-container application, which you have described in your docker-compose file.  
syntax: `docker-compose up`

### Run

Run command help to run a one-time command against a service. docker-compose run will start a new container to execute the command, not executed against a running container.  
Syntax: `docker-compose run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] [-l KEY=VALUE...] SERVICE [COMMAND] [ARGS...]`

### Scale

The docker-compose `scale` sets the number of containers to run for a service.  
syntax: `docker-compose scale <service> = <no of instances>`

> **Note:**
>
> -  **Do not name the container** to avoid errors: "Docker requires each container to have a unique name"
> -  The scale command is deprecated, instead Use the up command with the –scale flag.  
>    `docker-compose up --scale <service name> = <no of instances>`

### Exec

Docker exec is used to run commands in a running container, similarly docker-compose exec runs commands in your services.  
syntax: `docker-compose exec [options] [-e key=val...] service command [args...]`

### Images

The docker-compose `images` command help to listout images used/created by the containers.  
syntax: `docker-compose images`

### ps

The docker-compose `ps` command list out the containers with details like status, Port mapping etc, which is declared in docker-compose file.  
syntax: `docker-compose ps`

### Log

The docker-compose `logs` command help to see the service logs.  
syntax: `docker-compose logs`

### Port

The docker-compose port command print the public/network facing port for a service.  
syntax: `docker-compose port <service> <private_port>`

## Instructions

Validate the docker-compose file and view the compose file.

```sh
$ docker-compose config
```

Build the image and check the image just created

```sh
# Build the image
$ docker-compose build

# check the image just created
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        v1        8c6da3261ae4   1 mins ago   41MB
redis        latest    116cad43b6af   1 mins ago   117MB
mysql        5.7       dd6675b5cfea   1 mins ago   569MB

```

Bring up application, and run application in the background

```sh
# Bring up application, and run application in the background
$ docker-compose up -d
Creating network "lab1configbuilduprunscaleimagespslogport_default" with the default driver
Creating nginx_wb                                                ... done
Creating mysql_db                                                ... done
Creating lab1configbuilduprunscaleimagespslogport_redis-master_1 ... done

# Run webserver application in the background with name my_webserver
$ docker-compose run -d --name my_webserver webserver
Creating lab1configbuilduprunscaleimagespslogport_webserver_run ... done
my_webserver

# List services
$ docker-compose ps --service
webserver
dbserver
redis-master

# Scale service
$ docker-compose scale redis-master=3
WARNING: The scale command is deprecated. Use the up command with the --scale flag instead.
Creating lab1configbuilduprunscaleimagespslogport_redis-master_2 ... done
Creating lab1configbuilduprunscaleimagespslogport_redis-master_3 ... done
```

Check the images just created, check the running container and list all service

```sh
# Check the images just created
$ docker-compose images
                       Container                          Repository    Tag       Image Id       Size
-------------------------------------------------------------------------------------------------------
lab1configbuilduprunscaleimagespslogport_redis-master_1   redis        latest   116cad43b6af   117.1 MB
lab1configbuilduprunscaleimagespslogport_redis-master_2   redis        latest   116cad43b6af   117.1 MB
lab1configbuilduprunscaleimagespslogport_redis-master_3   redis        latest   116cad43b6af   117.1 MB
my_webserver                                              nginx        v1       8c6da3261ae4   41 MB
mysql_db                                                  mysql        5.7      dd6675b5cfea   568.6 MB
nginx_wb                                                  nginx        v1       8c6da3261ae4   41 MB

# Check the running container
$ docker-compose ps
                         Name                                        Command               State                    Ports
-------------------------------------------------------------------------------------------------------------------------------------------
lab1configbuilduprunscaleimagespslogport_redis-master_1   docker-entrypoint.sh redis ...   Up      0.0.0.0:38175->6379/tcp
lab1configbuilduprunscaleimagespslogport_redis-master_2   docker-entrypoint.sh redis ...   Up      0.0.0.0:46663->6379/tcp
lab1configbuilduprunscaleimagespslogport_redis-master_3   docker-entrypoint.sh redis ...   Up      0.0.0.0:41489->6379/tcp
my_webserver                                              /docker-entrypoint.sh ngin ...   Up      80/tcp
mysql_db                                                  docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb                                                  /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
```

Execute commands in different webservers

```sh
# Check the application before execute
$ curl http://localhost
...

$ docker-compose exec webserver  sh -c "echo 'Welcome to webserver' > /usr/share/nginx/html/index.html"

# Check the application after execute
$ curl http://localhost
Welcome to webserver
```

Check the external port bind to service web for port 80

```sh
$ docker-compose port webserver 80

0.0.0.0:80
```

Get the logs of the services

```sh
# Access the nginx while check the logs, to see live logs. (-f: follow (live logs))
$ docker-compose logs -f webserver
Attaching to nginx_wb
nginx_wb     | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
nginx_wb     | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
...

# Checking last two line logs for a service
$ docker-compose logs --tail=2
Attaching to lab1configbuilduprunscaleimagespslogport_redis-master_2, lab1configbuilduprunscaleimagespslogport_redis-master_3, lab1configbuilduprunscaleimagespslogport_redis-master_1, mysql_db, nginx_wb
redis-master_1  | 1:M 23 May 2023 09:20:11.892 # Server initialized
redis-master_1  | 1:M 23 May 2023 09:20:11.892 * Ready to accept connections
redis-master_3  | 1:M 23 May 2023 09:20:45.882 # Server initialized
redis-master_3  | 1:M 23 May 2023 09:20:45.883 * Ready to accept connections
mysql_db        | 2023-05-23T09:20:12.576175Z 0 [Note] mysqld: ready for connections.
mysql_db        | Version: '5.7.42'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
redis-master_2  | 1:M 23 May 2023 09:20:45.873 # Server initialized
redis-master_2  | 1:M 23 May 2023 09:20:45.873 * Ready to accept connections
nginx_wb        | 2023/05/23 09:20:11 [notice] 1#1: start worker process 34
nginx_wb        | 2023/05/23 09:20:11 [notice] 1#1: start worker process 35

```

---

> **Note:** See detail at
>
> -  [lab3_config_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/config_command.html)
> -  [lab4_build_command](hhttps://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/Lab_%231_Build_Command.html)
> -  [lab7_up_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/up_command.html)
> -  [lab8_images_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/images_command.html)
> -  [lab9_ps_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/ps_command.html)
> -  [lab15_logs_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/logs_command.html)
> -  [lab16_port_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/port_command.html)
> -  [lab17_run_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/run_command.html)
> -  [lab18_scale_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/scale_command.html)
> -  [laab19_exec_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/Lab_%234_Exec_Command.html)

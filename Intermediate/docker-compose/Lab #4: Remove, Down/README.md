# Lab #4: Remove, Down

## Introduction

### Remove

The docker-compose `rm` command helps to Remove stopped containers.  
syntax: `docker-compose rm`

> **Note:** You must stop container before remove it

### Down

The docker-compose `down` command helps to Stop and remove containers, networks, images, and volumes (`down` = `stop` + `remove`)  
syntax: `docker-compose down`

## Instructions

Start and Remove the container services

```sh
# Start the container service
$ docker-compose up -d
...
Creating mysql_db ... done
Creating nginx_wb ... done

# Checking container status
$ docker-compose ps
  Name                Command               State                    Ports
--------------------------------------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp

# Stop the container service
$ docker-compose stop
Stopping nginx_wb ... done
Stopping mysql_db ... done

$ docker-compose rm
Going to remove nginx_wb, mysql_db
Are you sure? [yN] y
Removing nginx_wb ... done
Removing mysql_db ... done

# Check the container service
$ docker-compose ps
Name   Command   State   Ports
------------------------------
```

Down the container service

```sh
# Start the container service
$ docker-compose up -d
...
Creating mysql_db ... done
Creating nginx_wb ... done

# Checking container status
$ docker-compose ps
  Name                Command               State                    Ports
--------------------------------------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp

# Stop and remove container service by Down command
$ docker-compose down
Stopping mysql_db ... done
Stopping nginx_wb ... done
Removing mysql_db ... done
Removing nginx_wb ... done
Removing network lab4removedown_default

# Checking container status
$ docker-compose ps
Name   Command   State   Ports
------------------------------
```

---

> **Note:** See detail at
>
> -  [lab21_remove_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/rm_command.html)
> -  [lab22_down_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/down_command.html)

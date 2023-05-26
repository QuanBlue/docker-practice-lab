# Lab #3: Kill, Stop, Start, Restart, Pause, Unpause

## Introduction

### Kill

The docker-compose `kill` command force stop service containers.  
syntax: `docker-compose kill`

### Stop

The docker-compose `stop` command help us to stop containers in service.  
syntax: `docker-compose stop`

> **Note:** different between `Kill` and `Stop` (See detail [here](https://www.baeldung.com/ops/docker-stop-vs-kill))
>
> -  **The docker stop command stops the container gracefully and provides a `safe way out`**. If a docker stop command fails to terminate a process within the specified timeout, the Docker issues a kill command implicitly and immediately
> -  **The docker kill command terminates the entry point process abruptly. The docker kill command causes an `unsafe exit`**. In some cases, the docker container runs with the volume mounted with the host. This might result in damage to the file system if there are still pending changes in memory when the main process stops.

### Start

The docker-compose `start` command help us to start containers of a service.  
syntax: `docker-compose start`

### Restart

The docker-compose `restart` command help us to restart the services.  
syntax: `docker-compose restart`

### Pause

The docker-compose `pause` command help to pause services.  
syntax: `docker-compose pause`

### Unpause

The docker-compose `unpause` command help to Unpauses paused containers of a service.  
syntax: `docker-compose unpause`

## Instructions

Start application in the background and check its status

```sh
# Start application
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
```

Kill, Stop and Start the container service

```sh
# Stop the webserver service
$ docker-compose stop webserver
Stopping nginx_wb ... done

# Kill the dbserver service
$ docker-compose kill dbserver
Killing mysql_db ... done

# Checking container status
$ docker-compose ps
  Name                Command                State     Ports
------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Exit 137
nginx_wb   /docker-entrypoint.sh ngin ...   Exit 0

# Start the services
$ docker-compose start
Starting webserver ... done
Starting dbserver  ... done

# Checking container status
$ docker-compose ps
  Name                Command               State                    Ports
--------------------------------------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
```

Stop and Restart the container service

```sh
# Stop the container service
$ docker-compose stop
Stopping nginx_wb ... done
Stopping mysql_db ... done

# Check the container service
$ docker-compose ps
  Name                Command               State    Ports
----------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Exit 0
nginx_wb   /docker-entrypoint.sh ngin ...   Exit 0

# Restart the container service
$ docker-compose restart
Restarting nginx_wb ... done
Restarting mysql_db ... done

# Check the container service
$ docker-compose ps
  Name                Command               State                    Ports
--------------------------------------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
```

Pause and Unpause the container service

```sh
# Pause the container service
$ docker-compose pause webserver
Pausing nginx_wb ... done

# Check the container service
$ docker-compose ps
  Name                Command               State                     Ports
---------------------------------------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Up       0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb   /docker-entrypoint.sh ngin ...   Paused   0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp

# Unpause the container service
$ docker-compose unpause webserver
Unpausing nginx_wb ... done

# Check the container service
$ docker-compose ps
  Name                Command               State                    Ports
--------------------------------------------------------------------------------------------
mysql_db   docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
nginx_wb   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
```

Remove the container service

```sh
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

Start application and Remove it

```sh
# Start application
docker-compose up -d

# Remove application
docker-compose down
```

---

> **Note:** See detail at
>
> -  [lab10_stop_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/stop_command.html)
> -  [lab11_start_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/start_command.html)
> -  [lab12_restart_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/restart_command.html)
> -  [lab13_pause_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/pause_command.html)
> -  [lab14_unpause_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/unpause_command.html)
> -  [lab20_kill_command](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/Lab_%237_Kill_Command.html)

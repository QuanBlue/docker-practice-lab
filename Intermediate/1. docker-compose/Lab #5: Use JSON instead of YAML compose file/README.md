# Lab #5: Use JSON instead of YAML compose file in Docker?

## Introduction

Yaml is a superset of json so any JSON file should be valid Yaml. To use a JSON file with Compose, specify the filename to use. In this lab we are going to bringup our same nginx and mysql containers using docker-compose file in json format.

syntax: `docker-compose -f <file_name> <command>`

> -f, --file FILE Specify an alternate compose file (default: docker-compose.yml)

## Instructions

Bringup the container and run as daemon

```sh
# Bringup the container and run as daemon
$ docker-compose -f my_app.json up -d
Creating network "lab5usejsoninsteadofyamlcomposef_default" with the default driver
Creating nginx_wb ... done

# Check the status of the container
$ docker-compose -f my_app.json ps
  Name                Command              State               Ports
--------------------------------------------------------------------------------
nginx_wb   /docker-entrypoint.sh ngin      Up      0.0.0.0:443->443/tcp,
           ...                                     0.0.0.0:80->80/tcp

# Check the logs of the container
$ docker-compose -f my_app.json logs --tail=2
Attaching to nginx_wb
nginx_wb     | 2023/05/23 13:57:02 [notice] 1#1: start worker process 34
nginx_wb     | 2023/05/23 13:57:02 [notice] 1#1: start worker process 35

# Check the application
$ curl http://localhost
...
```

---

> **Note:** See detail at
>
> -  [lab24_using_json_instead_of_yml](https://dockerlabs.collabnix.com/intermediate/workshop/DockerCompose/Lab_%2324_Use_JSON_instead_of_YAML_compose_file_in_Docker.html)

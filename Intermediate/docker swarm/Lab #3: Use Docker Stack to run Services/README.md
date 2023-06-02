# Lab #2: Deploy Services

## Assignment

-  Deploy services, load balancing
-  Inspect nodes, services,...
-  Scaling services

## Note

-  Only manager node can run Stack
-  `Stack` must be copy to manager node to run (not in host machine)

## Instruction

> Using 3 node create in [Lab #1](../Lab%20%231%3A%20Init%20and%20Manage%20Docker%20Swarm/)  
>  Using [ichte/swarmtest images](https://hub.docker.com/r/ichte/swarmtest/tags)

In node Manager: check node

```sh
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
f1dl5djcfhkhykpcele269rfh *   manager    Ready     Active         Leader           24.0.2
p9gvvpvqxrat5nu2quwckupfd     worker1    Ready     Active                          24.0.2
ss55qu877p9kxo1nomhvgsuqn     worker2    Ready     Active                          24.0.2
```

In host machine: copy `docker-compose.yml` to manager node at `/home`

```sh
# ----------- In Host machine -----------
# copy docker-compose.yml to manager node
$ docker cp docker-compose.yml manager:/home
Successfully copied 2.56kB to manager:/home

# ----------- In Manager node -----------
# check docker-compose.yml
$ cat /home/docker-compose.yml
version: "3.1"

services:
   dotnet:
      image: ichte/swarmtest:dotnet
      ports:
         - "8085:8085"
      deploy:
         replicas: 5
         resources:
            limits:
               cpus: "0.5"
               memory: 50M
            reservations:
               cpus: "0.25"
               memory: 20M
   php:
      ...
```

In manager node: deploy stack and check

```sh
# Deploy stack
$ docker stack deploy -c /home/docker-compose.yml mystack
Creating network mystack_default
Creating service mystack_php
Creating service mystack_dotnet

# Check stack
$ docker stack ls
NAME       SERVICES
mystack   2

# Inspect stack "mystack"
$ docker stack services mystack
ID             NAME             MODE         REPLICAS   IMAGE                    PORTS
t6waipnx5lbc   mystack_dotnet   replicated   5/5        ichte/swarmtest:dotnet   *:8085->8085/tcp
wttzdxpkvcm6   mystack_php      replicated   3/3        ichte/swarmtest:php      *:8086->8085/tcp

# Inspect service "mystack_dotnet"
$ docker service ps mystack_dotnet


# Inspect service "mystack_php"
$ docker service ps mystack_php

```

Let worker1 and worker2 in inspect mode, so it will auto show the stats of the containers that is running on the node

```sh
# ----------- In worker1 node -----------
$ docker stats

# ----------- In worker2 node -----------
$ docker stats
```

Open browser and access to address bellow to check service, refresh to see load balancing (you will see different `hostname`)

-  `http://<manager_ip_address>:8085`
-  `http://<manager_ip_address>:8086`

> **Note:** Get node ip address by using command `docker node inspect <node_name> | grep -i addr` in `Manager node`

In Manager node: Update stack (`docker-compose.yml`), you may use `vim` or `nano`
In this case, I change `resources`, `service's port` and `number of replicas`

```yml
# docker-compose.yml
version: "3.1"

services:
   dotnet:
      image: ichte/swarmtest:dotnet
      ports:
         - "8087:8085"
      deploy:
         replicas: 10
         resources:
            limits:
               cpus: "0.5"
               memory: 150M
            reservations:
               cpus: "0.25"
               memory: 50M
   php:
      image: ichte/swarmtest:php
      ports:
         - "8088:8085"
      deploy:
         replicas: 10
         resources:
            limits:
               cpus: "0.5"
               memory: 200M
            reservations:
               cpus: "0.25"
               memory: 100M
```

Update stack

```sh
# Update stack
$ docker stack deploy -c /home/docker-compose.yml mystack

#  ----------- In worker1 node  -----------
# It is in Inspect stats mode, so it will auto show the stats of the containers that is running on the node

#  ----------- In worker2 node  -----------
# It is in Inspect stats mode, so it will auto show the stats of the containers that is running on the node
```

Open browser and access to address bellow to check service, refresh to see load balancing (you will see different `hostname`)

-  `http://<manager_ip_address>:8085`
-  `http://<manager_ip_address>:8086`
-  `http://<manager_ip_address>:8087`
-  `http://<manager_ip_address>:8088`

> We has change port so we need to use new port

---

> **Note:** See detail at
>
> -  [XuanThuLab-youtube](https://www.youtube.com/watch?v=fedUjqpfT0A&ab_channel=XuanThuLab)
> -  [Docker-VPS](https://github.com/QuanBlue/Docker-VPS)

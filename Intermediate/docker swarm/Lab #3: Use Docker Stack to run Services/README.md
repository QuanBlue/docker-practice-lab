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
mz4qnbpsujlscvsdagaqd8rod *   manager    Ready     Active         Leader           24.0.2
i2nty40m59z0fkspp8bh5hmqd     worker1    Ready     Active                          24.0.2
111zpvn3p7kba8usupbfj2vic     worker2    Ready     Active                          24.0.2
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
qlqnoak64r54   mystack_dotnet   replicated   5/5        ichte/swarmtest:dotnet   *:8085->8085/tcp
45g88wglclf8   mystack_php      replicated   3/3        ichte/swarmtest:php      *:8086->8085/tcp

# Inspect service "mystack_dotnet"
$ docker service ps mystack_dotnet
ID             NAME               IMAGE                    NODE      DESIRED STATE   CURRENT STATE            ERROR     PORTS
hs4qvtzz36tg   mystack_dotnet.1   ichte/swarmtest:dotnet   worker2   Running         Running 9 minutes ago
b2f52509wx0l   mystack_dotnet.2   ichte/swarmtest:dotnet   manager   Running         Running 11 minutes ago
pbyjmssqieaj   mystack_dotnet.3   ichte/swarmtest:dotnet   worker1   Running         Running 7 minutes ago
nt2sg03pzqrp   mystack_dotnet.4   ichte/swarmtest:dotnet   worker2   Running         Running 9 minutes ago
w9vbv4sgjz8k   mystack_dotnet.5   ichte/swarmtest:dotnet   worker1   Running         Running 7 minutes ago

# Inspect service "mystack_php"
$ docker service ps mystack_php
ID             NAME            IMAGE                 NODE      DESIRED STATE   CURRENT STATE           ERROR     PORTS
j6nu0lxru9cu   mystack_php.1   ichte/swarmtest:php   manager   Running         Running 7 minutes ago
4fe9n0e1f4t1   mystack_php.2   ichte/swarmtest:php   worker2   Running         Running 8 minutes ago
q0qvg51u08qi   mystack_php.3   ichte/swarmtest:php   worker1   Running         Running 9 minutes ago
```

Let worker1 and worker2 in inspect mode, so it will auto show the stats of the containers that is running on the node

```sh
# ----------- In worker1 node -----------
$ docker stats
CONTAINER ID   NAME                                         CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS
04bd574e1f7b   mystack_php.3.q0qvg51u08qi4ew3w83fsrzp2      0.01%     4.613MiB / 150MiB   3.08%     0B / 0B   0B / 0B     1
65d65ce8367b   mystack_dotnet.5.w9vbv4sgjz8kddy2oen6krilf   0.11%     18.28MiB / 50MiB    36.55%    0B / 0B   0B / 0B     25
d52214782177   mystack_dotnet.3.pbyjmssqieajpzbpdua6xwel6   0.11%     18.25MiB / 50MiB    36.49%    0B / 0B   0B / 0B     25

# ----------- In worker2 node -----------
$ docker stats
CONTAINER ID   NAME                                         CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS
194073b975b0   mystack_dotnet.4.nt2sg03pzqrp0yefmryrp2xa0   0.05%     18.27MiB / 50MiB    36.53%    0B / 0B   0B / 0B     25
af9f95c487f5   mystack_dotnet.1.hs4qvtzz36tgz8p97wxxfwuu2   0.07%     18.23MiB / 50MiB    36.46%    0B / 0B   0B / 0B     25
9860a04c9bdb   mystack_php.2.4fe9n0e1f4t1mdu680s0ltp58      0.01%     4.621MiB / 150MiB   3.08%     0B / 0B   0B / 0B     1
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
Updating service mystack_dotnet (id: qlqnoak64r5490q1514bnati0)
image ichte/swarmtest:dotnet could not be accessed on a registry to record
its digest. Each node will access ichte/swarmtest:dotnet independently,
possibly leading to different nodes running different
versions of the image.

Updating service mystack_php (id: 45g88wglclf8lr8p432hprq3c)

# Check updated stack
$ docker stack services mystack
ID             NAME             MODE         REPLICAS   IMAGE                    PORTS
qlqnoak64r54   mystack_dotnet   replicated   10/10      ichte/swarmtest:dotnet   *:8087->8085/tcp
45g88wglclf8   mystack_php      replicated   10/10      ichte/swarmtest:php      *:8088->8085/tcp


#  ----------- In worker1 node  -----------
# It is in Inspect stats mode, so it will auto show the stats of the containers that is running on the node
CONTAINER ID   NAME                                         CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS
b7801ff00292   mystack_dotnet.7.ynvftpghog5miezw5umzspb96   0.07%     18.04MiB / 150MiB   12.02%    0B / 0B   0B / 0B     26
2ca9c4d11941   mystack_php.6.fbakiui0p7l4mm345vhfzg59s      0.00%     4.559MiB / 200MiB   2.28%     0B / 0B   0B / 0B     1
31de1a832b41   mystack_php.9.xxvi3fkgpj119syjf85gd77e0      0.00%     4.57MiB / 200MiB    2.29%     0B / 0B   0B / 0B     1
d861ab5a1cf6   mystack_php.3.ljq0hz1y4ncp15bsjj8jtrh9r      0.00%     4.555MiB / 200MiB   2.28%     0B / 0B   0B / 0B     1
a2da7d73dc24   mystack_dotnet.3.p6ar0w873554jbhkvjpc5y40l   0.12%     17.82MiB / 150MiB   11.88%    0B / 0B   0B / 0B     26
0277efd11760   mystack_dotnet.5.4e3e8byuq0gsojx0kbbbkqvna   1.16%     17.76MiB / 150MiB   11.84%    0B / 0B   0B / 0B     26

#  ----------- In worker2 node  -----------
# It is in Inspect stats mode, so it will auto show the stats of the containers that is running on the node
CONTAINER ID   NAME                                         CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS
83a56a272f8e   mystack_dotnet.8.s7euuq568xfjxpk87s3eojt0v   0.09%     18.07MiB / 150MiB   12.05%    0B / 0B   0B / 0B     25
8aea5e9533ca   mystack_php.5.mslzal8gr961yyjrk59d6ympl      0.00%     4.551MiB / 200MiB   2.28%     0B / 0B   0B / 0B     1
7a5373dc7b9a   mystack_php.4.fsxs94wxnburryxyhhpsv16y4      0.01%     4.562MiB / 200MiB   2.28%     0B / 0B   0B / 0B     1
31d60b671095   mystack_php.8.8qe5fafxwsus02sfk3vh4qmzd      0.00%     4.555MiB / 200MiB   2.28%     0B / 0B   0B / 0B     1
9c080118fb64   mystack_php.10.ea17h0qsebxv5sfou1v6173us     0.00%     4.559MiB / 200MiB   2.28%     0B / 0B   0B / 0B     1
4e6936f2209b   mystack_dotnet.1.ogmphizwbf938jpxvu85z7wn4   0.11%     17.89MiB / 150MiB   11.93%    0B / 0B   0B / 0B     26
483cc955bd9e   mystack_dotnet.4.9haox7mpvxs6v1yflwqxruh1c   0.11%     17.85MiB / 150MiB   11.90%    0B / 0B   0B / 0B     26
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

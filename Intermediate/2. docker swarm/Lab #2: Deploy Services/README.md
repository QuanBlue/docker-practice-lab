# Lab #2: Deploy Services

## Assignment

-  Deploy services, load balancing
-  Inspect nodes, services,...
-  Scaling services

## Note

-  Only manager node can deploy service
-  Node Manager will auto load balancing
-  Worker node can only join the swarm and run service

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

In node Manager: create service `ichte/swarmtest:node` with 5 replicas

```sh
$ docker service create --name node-service --replicas 5 --publish 8085:8085 ichte/swarmtest:node
```

Check running service in each node

```sh
# ----------- In Manager node -----------
$ docker service ls
ID             NAME           MODE         REPLICAS   IMAGE                  PORTS
a8covbkibh02   node-service   replicated   5/5        ichte/swarmtest:node   *:8085->8085/tcp

$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS      NAMES
e7aaa02409de   ichte/swarmtest:node   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   8085/tcp   node-service.5.pb1odxpcwhqj2zrkbg87p4cf2

$ docker service ps node-service
ID             NAME             IMAGE                  NODE      DESIRED STATE   CURRENT STATE           ERROR     PORTS
kfq1jrkpi8dp   node-service.1   ichte/swarmtest:node   worker1   Running         Running 5 minutes ago
a87wlvm319o3   node-service.2   ichte/swarmtest:node   worker2   Running         Running 5 minutes ago
shzq7kwgo37g   node-service.3   ichte/swarmtest:node   worker1   Running         Running 5 minutes ago
bgsugcx5pgvw   node-service.4   ichte/swarmtest:node   worker2   Running         Running 5 minutes ago
pb1odxpcwhqj   node-service.5   ichte/swarmtest:node   manager   Running         Running 5 minutes ago

# ----------- In Worker node 1 -----------
$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS      NAMES
616f3b427d1d   ichte/swarmtest:node   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   8085/tcp   node-service.1.kfq1jrkpi8dpvmtezo5o52ztm
b97aa06c97c2   ichte/swarmtest:node   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   8085/tcp   node-service.3.shzq7kwgo37ge4iill5yetpyn

# ----------- In Worker node 2 -----------
$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS      NAMES
dec2b4e53b3f   ichte/swarmtest:node   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   8085/tcp   node-service.4.bgsugcx5pgvwxd1pehy9tsqxv
f5c4dce0f443   ichte/swarmtest:node   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   8085/tcp   node-service.2.a87wlvm319o3mqk7clz1hnikf
```

Open browser and access to `http://<manager_ip_address>:8085` to check service, refresh to see load balancing (you will see different `hostname`)

> **Note:** Get node ip address by using command `docker node inspect <node_name> | grep -i addr` in `Manager node`
>
> ```sh
> $ docker node inspect manager | grep -i addr
>            "Addr": "172.17.0.2"
>            "Addr": "172.17.0.2:2377"
> $ docker node inspect worker1 | grep -i addr
>            "Addr": "172.17.0.3"
> $ docker node inspect worker2 | grep -i addr
>            "Addr": "172.17.0.4"
> ```

**Remove container services** in `worker2`

```sh
# In worker2 node: Remove container
$ docker rm -f $(docker ps -q)

dec2b4e53b3f
f5c4dce0f443

# In manager node: Check service
$ docker service ps node-service

ID             NAME                 IMAGE                  NODE      DESIRED STATE   CURRENT STATE            ERROR                         PORTS
kfq1jrkpi8dp   node-service.1       ichte/swarmtest:node   worker1   Running         Running 14 minutes ago
b9r1g0khsgkx   node-service.2       ichte/swarmtest:node   worker2   Running         Running 48 seconds ago
a87wlvm319o3    \_ node-service.2   ichte/swarmtest:node   worker2   Shutdown        Failed 54 seconds ago    "task: non-zero exit (137)"
shzq7kwgo37g   node-service.3       ichte/swarmtest:node   worker1   Running         Running 14 minutes ago
twfd93cqbl3o   node-service.4       ichte/swarmtest:node   worker2   Running         Running 48 seconds ago
bgsugcx5pgvw    \_ node-service.4   ichte/swarmtest:node   worker2   Shutdown        Failed 54 seconds ago    "task: non-zero exit (137)"
pb1odxpcwhqj   node-service.5       ichte/swarmtest:node   manager   Running         Running 13 minutes ago
```

> You will see `node-service.2` and `node-service.4` in node `worker2` is shutdown and appear 2 services in node `worker2` is `node-service.2` and `node-service.4`
>
> -  It is the technique of Docker Swarm `ensure service availability` when node/container is down/die (new container will be created in other or in this node to ensure service availability and load balancing)

In Manager node: Watch logs of service

```sh
$ docker service logs node-service

node-service.5.pb1odxpcwhqj@manager    | Node  App, port 8085, hostname=e7aaa02409de
node-service.3.shzq7kwgo37g@worker1    | Node  App, port 8085, hostname=b97aa06c97c2
node-service.1.kfq1jrkpi8dp@worker1    | Node  App, port 8085, hostname=616f3b427d1d
node-service.4.twfd93cqbl3o@worker2    | Node  App, port 8085, hostname=49f5ba54dba6
node-service.2.b9r1g0khsgkx@worker2    | Node  App, port 8085, hostname=0232d0cb871d
```

To inspect stats of nodes

```sh
#  ----------- In work1 node  -----------
$ docker stats
CONTAINER ID   NAME                                       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O         BLOCK I/O   PIDS
616f3b427d1d   node-service.1.kfq1jrkpi8dpvmtezo5o52ztm   0.00%     5.332MiB / 15.46GiB   0.03%     1.52kB / 894B   0B / 0B     7
b97aa06c97c2   node-service.3.shzq7kwgo37ge4iill5yetpyn   0.00%     5.344MiB / 15.46GiB   0.03%     1.56kB / 936B   0B / 0B     7

#  ----------- In work2 node  -----------
$ docker stats
CONTAINER ID   NAME                                       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O   BLOCK I/O   PIDS
49f5ba54dba6   node-service.4.twfd93cqbl3o4rs254nkwpcb7   0.00%     5MiB / 15.46GiB       0.03%     0B / 0B   0B / 0B     7
0232d0cb871d   node-service.2.b9r1g0khsgkxwdznqt45f8jpy   0.00%     5.004MiB / 15.46GiB   0.03%     0B / 0B   0B / 0B     7
```

> **stats:** will always show the stats of the containers that is running on the node you are on and update every second

Scale service with 10 replicas and inspect service

```sh
#  ----------- In manager node  -----------
$ docker service scale node-service=10
node-service scaled to 10
overall progress: 10 out of 10 tasks
1/10: running
2/10: running
3/10: running
4/10: running
5/10: running
6/10: running
7/10: running
8/10: running
9/10: running
10/10: running
verify: Service converged

$ docker service ls
ID             NAME           MODE         REPLICAS   IMAGE                  PORTS
a8covbkibh02   node-service   replicated   10/10      ichte/swarmtest:node   *:8085->8085/tcp

$ docker service ps node-service
ID             NAME                 IMAGE                  NODE      DESIRED STATE   CURRENT STATE                ERROR                         PORTS
kfq1jrkpi8dp   node-service.1       ichte/swarmtest:node   worker1   Running         Running 35 minutes ago
b9r1g0khsgkx   node-service.2       ichte/swarmtest:node   worker2   Running         Running 21 minutes ago
a87wlvm319o3    \_ node-service.2   ichte/swarmtest:node   worker2   Shutdown        Failed 21 minutes ago        "task: non-zero exit (137)"
shzq7kwgo37g   node-service.3       ichte/swarmtest:node   worker1   Running         Running 35 minutes ago
twfd93cqbl3o   node-service.4       ichte/swarmtest:node   worker2   Running         Running 21 minutes ago
bgsugcx5pgvw    \_ node-service.4   ichte/swarmtest:node   worker2   Shutdown        Failed 21 minutes ago        "task: non-zero exit (137)"
pb1odxpcwhqj   node-service.5       ichte/swarmtest:node   manager   Running         Running 34 minutes ago
6lt5hljn5z5j   node-service.6       ichte/swarmtest:node   manager   Running         Running about a minute ago
wlwczx1m6n80   node-service.7       ichte/swarmtest:node   worker1   Running         Running about a minute ago
ne9uz5qt8ten   node-service.8       ichte/swarmtest:node   worker2   Running         Running about a minute ago
mjbqegna03k2   node-service.9       ichte/swarmtest:node   manager   Running         Running about a minute ago
25s041x8104h   node-service.10      ichte/swarmtest:node   manager   Running         Running about a minute ago


#  ----------- In worker1 node  -----------
# It is in Inspect stats mode, so it will auto show the stats of the containers that is running on the node
CONTAINER ID   NAME                                       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O         BLOCK I/O   PIDS
616f3b427d1d   node-service.1.kfq1jrkpi8dpvmtezo5o52ztm   0.00%     5.332MiB / 15.46GiB   0.03%     1.52kB / 894B   0B / 0B     7
b97aa06c97c2   node-service.3.shzq7kwgo37ge4iill5yetpyn   0.00%     5.344MiB / 15.46GiB   0.03%     1.56kB / 936B   0B / 0B     7
632c37a452eb   node-service.7.wlwczx1m6n80u15348qm3ejb0   0.00%     4.996MiB / 15.46GiB   0.03%     0B / 0B         0B / 0B     7

#  ----------- In worker2 node  -----------
# It is in Inspect stats mode, so it will auto show the stats of the containers that is running on the node
CONTAINER ID   NAME                                       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O   BLOCK I/O   PIDS
49f5ba54dba6   node-service.4.twfd93cqbl3o4rs254nkwpcb7   0.00%     5MiB / 15.46GiB       0.03%     0B / 0B   0B / 0B     7
0232d0cb871d   node-service.2.b9r1g0khsgkxwdznqt45f8jpy   0.00%     5.004MiB / 15.46GiB   0.03%     0B / 0B   0B / 0B     7
f83c08e539b8   node-service.8.ne9uz5qt8tenmuk80o1fven87   0.00%     4.996MiB / 15.46GiB   0.03%     0B / 0B   0B / 0B     7
```

> **Note:** See detail at
>
> -  [XuanThuLab-youtube](https://www.youtube.com/watch?v=QcURXYCZSuY&list=LL&index=4&t=1082s&ab_channel=XuanThuLab)
> -  [Docker-VPS](https://github.com/QuanBlue/Docker-VPS)

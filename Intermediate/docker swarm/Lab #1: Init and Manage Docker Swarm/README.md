# Lab #1: Init and Manage Docker Swarm

## Introduction

<div align="center">
    <img src="./assets/swarm%20cluster.png" height="300" alt="swarm cluster">
    Swarm cluster
</div>

## Assignment

-  Create the Swarm
-  Inspect the Swarm, Node
-  Add/Remove node to/from the Swarm
-  Let node leave the Swarm and rejoin the Swarm
-  Let node stop and start

## Instructions

> **Note:** How to create Docker in Docker (DinD) container: [Docker-VPS](https://github.com/QuanBlue/Docker-VPS)

Create `1 manager` and `2 worker` node(in 3 distinct terminal) and go inside, interact with them:

```bash
# terminal 1: manager
$ docker run -d --privileged --hostname manager --name manager docker:dind
$ docker exec -it manager /bin/sh

# terminal 2: worker1
$ docker run -d --privileged --hostname worker1 --name worker1 docker:dind
$ docker exec -it worker1 /bin/sh

# terminal 3: worker2
$ docker run -d --privileged --hostname worker2 --name worker2 docker:dind
$ docker exec -it worker2 /bin/sh
```

In Manager node: **init Swarm** and check it status:

```bash
# init swarm
$ docker swarm init

Swarm initialized: current node (7w9io9cg2v0ih8lb07wj315g0) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-06syru5vxn2nuvnqrmr7h89tcimqvinuvzxndtwuep2odf6qp3-90irzhvuvxrloe9w4hhmg05vp 172.17.0.2:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

# check status
$ docker info
...
Swarm: active
  NodeID: 7w9io9cg2v0ih8lb07wj315g0
  Is Manager: true
  ClusterID: 1mmcnbxqqoa01ovl125dv6gk2
  Managers: 1
  Nodes: 1
...

# List node in the Swarm
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
```

> Remember join token: `docker swarm join --token SWMTKN-1-06syru5vxn2nuvnqrmr7h89tcimqvinuvzxndtwuep2odf6qp3-90irzhvuvxrloe9w4hhmg05vp 172.17.0.2:2377`

In Worker nodes: **join the Swarm** - In Manager node: list node in the Swarm

```sh
# join the swarm (worker node 1)
docker swarm join --token SWMTKN-1-06syru5vxn2nuvnqrmr7h89tcimqvinuvzxndtwuep2odf6qp3-90irzhvuvxrloe9w4hhmg05vp 172.17.0.2:2377
This node joined a swarm as a worker.

# join the swarm (worker node 2)
docker swarm join --token SWMTKN-1-06syru5vxn2nuvnqrmr7h89tcimqvinuvzxndtwuep2odf6qp3-90irzhvuvxrloe9w4hhmg05vp 172.17.0.2:2377
This node joined a swarm as a worker.

# List node in the Swarm (in manager node)
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
izapweuqdllr6g2z9nxtauebx     worker1    Ready     Active                          24.0.2
vzcxu8sd70c03yu8nkbvinl6y     worker2    Ready     Active                          24.0.2
```

Let worker node 2 **leave** and **rejoin the Swarm**

```sh
# leave the swarm (worker node 2)
$ docker swarm leave
Node left the swarm.

# check node in the Swarm (in manager node)
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
izapweuqdllr6g2z9nxtauebx     worker1    Ready     Active                          24.0.2
vzcxu8sd70c03yu8nkbvinl6y     worker2    Down      Active                          24.0.2

# rejoin the swarm (in worker node 2)
docker swarm join --token SWMTKN-1-06syru5vxn2nuvnqrmr7h89tcimqvinuvzxndtwuep2odf6qp3-90irzhvuvxrloe9w4hhmg05vp 172.17.0.2:2377
This node joined a swarm as a worker.

# check node in the Swarm (in manager node)
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
izapweuqdllr6g2z9nxtauebx     worker1    Ready     Active                          24.0.2
62sikzh3njpkr4ydvp8q090gt     worker2    Ready     Active                          24.0.2
vzcxu8sd70c03yu8nkbvinl6y     worker2    Down      Active                          24.0.2
```

In Manager node: **remove down node** in Swarm and check in Manager node

```sh
# remove down node
$ docker node rm vz
vz

# check node in the Swarm
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
izapweuqdllr6g2z9nxtauebx     worker1    Ready     Active                          24.0.2
62sikzh3njpkr4ydvp8q090gt     worker2    Ready     Active                          24.0.2
```

Let worker node 1 **stop** and **start**, check the Swarm

```sh
# Stop worker node 1 (in host machine)
$ docker stop worker1
worker1

# check node in the Swarm (in manager node)
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
izapweuqdllr6g2z9nxtauebx     worker1    Down      Active                          24.0.2
62sikzh3njpkr4ydvp8q090gt     worker2    Ready     Active                          24.0.2

# Start worker node 1 (in host machine)
$ docker start worker1
worker1

# check node in the Swarm (in manager node)
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
7w9io9cg2v0ih8lb07wj315g0 *   manager    Ready     Active         Leader           24.0.2
izapweuqdllr6g2z9nxtauebx     worker1    Ready     Active                          24.0.2
62sikzh3njpkr4ydvp8q090gt     worker2    Ready     Active                          24.0.2
```

---

> **Note:** See detail at
>
> -  [XuanThuLab-youtube](https://www.youtube.com/watch?v=6a529awrb4Q&list=LL&index=13&t=314s&ab_channel=XuanThuLab)
> -  [Docker-VPS](https://github.com/QuanBlue/Docker-VPS)

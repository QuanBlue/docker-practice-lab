# Lab #4: Overlay network

## Introduction

<div align="center">
    <img src="./assets/Swarm%20network_not_ovelay.png"  alt="swarm cluster">
    <i>Service containers not connect to overlay network</i>
</div>

By default, if service containers created by **Image that do not Expose port**, so **containers in each VPS are isolated**, and **can not communicate with containers from other VPSs**. To make them communicate, we need to use `Overlay` network.

<div align="center">
    <img src="./assets/Swarm%20network_ovelay.png" alt="swarm cluster">
    <i>Service containers connect to overlay network</i>
</div>

Otherwise, containers in each VPS communicate with containers from other VPSs by `Overlay network`, default is `Ingress` network.

## Assignment

-  Create service without expose port, ping to container from other VPS
-  Create service with expose port, ping to container from other VPS
-  Create new overlay network, Create service with this network

## Instructions

### Distinguish service using Bridge network and Overlay network

In node Manager:

-  Create `service1` with image `busybox:latest`, not expose port
-  Create `service2` with image `busybox:latest`, expose port 80

```sh
# deploy service1
$ docker service create --replicas 3 --name service1 busybox top
j45j8i5ca1za4bguo93x2c0kd
overall progress: 3 out of 3 tasks
1/3: running
2/3: running
3/3: running
verify: Service converged

# deploy service2
$ docker service create --replicas 3 --name service2 -p 8888:80 busybox top
p3rsloiotkg23f3nyyhrjkzed
overall progress: 3 out of 3 tasks
1/3: running
2/3: running
3/3: running
verify: Service converged

# check created services
$ docker service ls
ID             NAME       MODE         REPLICAS   IMAGE            PORTS
j45j8i5ca1za   service1   replicated   3/3        busybox:latest
p3rsloiotkg2   service2   replicated   3/3        busybox:latest   *:8888->80/tcp
```

Get IP address of containers in `service1`

```sh
# ----------- In Manager node -----------
# check containers in service1
$ docker ps
CONTAINER ID   IMAGE            COMMAND   CREATED         STATUS         PORTS     NAMES
13f789bd76cf   busybox:latest   "top"     4 minutes ago   Up 4 minutes             service2.3.tpv07cg8pf68c8a8h8bz4hcfc
697ddd40a6f1   busybox:latest   "top"     4 minutes ago   Up 4 minutes             service1.3.ou4dw5mdtbgeirg6yocvd3uha

# get ip address of container service1.3.ou4dw5mdtbgeirg6yocvd3uha
$ docker inspect 697ddd40a6f1
[
    {
        ...
        "NetworkSettings": {
            ....
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "1d60e8824145e9869218b0de9a210dc66f83b0494c02993a12be80cc980c47c8",
                    "EndpointID": "1e0cb9103d7c2c4ad51512a1b0c4e5b884ab5b8e545de558a52cc4cdf4797566",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

# ----------- In worker1 node -----------
# check containers in service1
$ docker ps
CONTAINER ID   IMAGE            COMMAND   CREATED              STATUS              PORTS     NAMES
0c811e0f887f   busybox:latest   "top"     About a minute ago   Up 58 seconds                 service2.1.g4yb6k62ghx7u1fp9tsu67ls4
bc262c6b7b43   busybox:latest   "top"     About a minute ago   Up About a minute             service1.1.hon7yn33sx2nzt59cf0zdjfm6

# get ip address of container service1.1.hon7yn33sx2nzt59cf0zdjfm6
$ docker inspect bc262c6b7b43
[
    {
        ...
        "NetworkSettings": {
            ....
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "dd6d53c07a3be0f9129b2d1e9ed81118c36490601b89ada97222773e5d1950af",
                    "EndpointID": "9cf3c556224ebd55d8c1ce229ad83dc661f0d27899f3fb995eee5fc8ac1419e9",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

# ----------- In worker2 node -----------
# check containers in service1
$ docker ps
CONTAINER ID   IMAGE            COMMAND   CREATED              STATUS              PORTS     NAMES
47ea88f75b32   busybox:latest   "top"     About a minute ago   Up About a minute             service2.2.qm5efujti3t6l5mb9sv4ebk0p
8dc33165077c   busybox:latest   "top"     About a minute ago   Up About a minute             service1.2.o9825ss1d7cgblacqlgsp7qp6

# get ip address of container service1.2.o9825ss1d7cgblacqlgsp7qp6
$ docker inspect 8dc33165077c
[
    {
        ...
        "NetworkSettings": {
            "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "f98dcae6d065ef516375a66558d213560f6c79ceeb5b517d391966a584b11eaf",
                    "EndpointID": "620b81a5d27d2b2413811296abbef2ed405941aac5a004160251a09b77642b6d",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
        }
    }
]
```

> We can see:
>
> -  `Service1` not expose port so containers in this service have network `Bridge`
> -  They have _different NetworkID and EndpointID_ but _same Gateway and IP address_
>
> -> They **can not communicate with each other**

Get IP address of containers in `service2`

```sh
# ----------- In manager node -----------
# check containers in service2
$ docker ps
CONTAINER ID   IMAGE            COMMAND   CREATED         STATUS         PORTS     NAMES
13f789bd76cf   busybox:latest   "top"     4 minutes ago   Up 4 minutes             service2.3.tpv07cg8pf68c8a8h8bz4hcfc
697ddd40a6f1   busybox:latest   "top"     4 minutes ago   Up 4 minutes             service1.3.ou4dw5mdtbgeirg6yocvd3uha

# get ip address of container service2.3.tpv07cg8pf68c8a8h8bz4hcfc
$ docker inspect 13f789bd76cf
[
    {
        ...
        "NetworkSettings": {
            ....
            "Networks": {
                "ingress": {
                    "IPAMConfig": {
                        "IPv4Address": "10.0.0.11"
                    },
                    "Links": null,
                    "Aliases": [
                        "13f789bd76cf"
                    ],
                    "NetworkID": "0jlupdxspyhup6dcmvhis6kh3",
                    "EndpointID": "9100e8e967305e51c03e6a479b884294a353a83e12f4be058c1c28ae40ab485b",
                    "Gateway": "",
                    "IPAddress": "10.0.0.11",
                    "IPPrefixLen": 24,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:0a:00:00:0b",
                    "DriverOpts": null
                }
            }
        }
    }
]

# ----------- In worker1 node -----------
# check containers in service2
$ docker ps
CONTAINER ID   IMAGE            COMMAND   CREATED              STATUS              PORTS     NAMES
0c811e0f887f   busybox:latest   "top"     About a minute ago   Up 58 seconds                 service2.1.g4yb6k62ghx7u1fp9tsu67ls4
bc262c6b7b43   busybox:latest   "top"     About a minute ago   Up About a minute             service1.1.hon7yn33sx2nzt59cf0zdjfm6

# get ip address of container service2.1.g4yb6k62ghx7u1fp9tsu67ls4
$ docker inspect 0c811e0f887f
[
    {
        ...
        "NetworkSettings": {
            ....
            "Networks": {
                "ingress": {
                    "IPAMConfig": {
                        "IPv4Address": "10.0.0.12"
                    },
                    "Links": null,
                    "Aliases": [
                        "0c811e0f887f"
                    ],
                    "NetworkID": "0jlupdxspyhup6dcmvhis6kh3",
                    "EndpointID": "5c7aa695efff23d0baf0746d05e48cbd335f92d006b779075113bbbe6baa1c24",
                    "Gateway": "",
                    "IPAddress": "10.0.0.12",
                    "IPPrefixLen": 24,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:0a:00:00:0c",
                    "DriverOpts": null
                }
            }
        }
    }
]

# ----------- In worker2 node -----------
# check containers in service2
$ docker ps
CONTAINER ID   IMAGE            COMMAND   CREATED              STATUS              PORTS     NAMES
47ea88f75b32   busybox:latest   "top"     About a minute ago   Up About a minute             service2.2.qm5efujti3t6l5mb9sv4ebk0p
8dc33165077c   busybox:latest   "top"     About a minute ago   Up About a minute             service1.2.o9825ss1d7cgblacqlgsp7qp6

# get ip address of container service2.2.qm5efujti3t6l5mb9sv4ebk0p
$ docker inspect 47ea88f75b32
[
    {
        ...
        "NetworkSettings": {
             "ingress": {
                    "IPAMConfig": {
                        "IPv4Address": "10.0.0.10"
                    },
                    "Links": null,
                    "Aliases": [
                        "47ea88f75b32"
                    ],
                    "NetworkID": "0jlupdxspyhup6dcmvhis6kh3",
                    "EndpointID": "c0285ddc422d186e672a4605192cf55294b457c4a854210404e53e3361a16b38",
                    "Gateway": "",
                    "IPAddress": "10.0.0.10",
                    "IPPrefixLen": 24,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:0a:00:00:0a",
                    "DriverOpts": null
                }
        }
    }
]
```

> We can see IP address of each container of `service2` in 3 nodes:
>
> -  Manager: `10.0.0.11`
> -  Worker1: `10.0.0.12`
> -  worker2: `10.0.0.10`

In Manager node: Enter container `13f789bd76cf` and ping to container `0c811e0f887f` in worker1

```sh
$ docker exec -it 13f789bd76cf sh

$ ping 10.0.0.12
PING 10.0.0.12 (10.0.0.12): 56 data bytes
64 bytes from 10.0.0.12: seq=0 ttl=64 time=0.646 ms
64 bytes from 10.0.0.12: seq=1 ttl=64 time=0.262 ms
64 bytes from 10.0.0.12: seq=2 ttl=64 time=0.250 ms
64 bytes from 10.0.0.12: seq=3 ttl=64 time=0.258 ms
^C
--- 10.0.0.12 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.217/0.293/0.646 ms
```

In worker2 node: Enter container `47ea88f75b32` and ping to container `13f789bd76cf` in manager

```sh
$ docker exec -it 47ea88f75b32 sh

$ ping 10.0.0.11
PING 10.0.0.11 (10.0.0.11): 56 data bytes
64 bytes from 10.0.0.11: seq=0 ttl=64 time=0.474 ms
64 bytes from 10.0.0.11: seq=1 ttl=64 time=0.237 ms
64 bytes from 10.0.0.11: seq=2 ttl=64 time=0.267 ms
64 bytes from 10.0.0.11: seq=3 ttl=64 time=0.258 ms
^C
--- 10.0.0.11 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.237/0.309/0.474 ms
```

> We can see that containers in `service2` **can ping to each other in 3 nodes**

### Create network overlay and deploy service with new network overlay

In node manager: Create network overlay

```sh
# create network overlay
$ docker network create --driver overlay my_overlay_network
j7ax4skdjej4ewvpswv8dxtfl

# check new network overlay
$ docker network ls
NETWORK ID     NAME                 DRIVER    SCOPE
1d60e8824145   bridge               bridge    local
d1c42b22b4cd   docker_gwbridge      bridge    local
69a87b44067b   host                 host      local
0jlupdxspyhu   ingress              overlay   swarm
j7ax4skdjej4   my_overlay_network   overlay   swarm
b1c62fe21591   none                 null      local
```

In node manager: Create service with new created network overlay

```sh
# create service with new network overlay
$ docker service create --replicas 3 --name overlay_service -p 9999:80 --network my_overlay_network busybox top
qwwtrbsppc9tr27eu2vemrddb
overall progress: 3 out of 3 tasks
1/3: running   [==================================================>]
2/3: running   [==================================================>]
3/3: running   [==================================================>]
verify: Service converged

# check IP address of containers in service overlay_service
$ docker network inspect my_overlay_network
[
    {
        "Name": "my_overlay_network",
        "Id": "j7ax4skdjej4ewvpswv8dxtfl",
        "Created": "2023-06-03T10:54:20.885838041Z",
        "Scope": "swarm",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "10.0.1.0/24",
                    "Gateway": "10.0.1.1"
                }
            ]
        },
        ...
        "Peers": [
            {
                "Name": "d37ddf1a3244",
                "IP": "172.17.0.4"
            },
            {
                "Name": "a8c4467dc607",
                "IP": "172.17.0.3"
            },
            {
                "Name": "df5eb03043b1",
                "IP": "172.17.0.2"
            }
        ]
    }
]
```

> We can see IP address of each container of service `overlay_service` in 3 nodes is distinct so we can ping to each other

---

> **Note:** See detail at
>
> -  [XuanThuLab-youtube](https://www.youtube.com/watch?v=aSQjoCqOx7k&t=1s&ab_channel=XuanThuLab)
> -  [Docker-VPS](https://github.com/QuanBlue/Docker-VPS)

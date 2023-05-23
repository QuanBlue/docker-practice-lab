# Lab #8: Networking

## Assignment

-  Create bridge network
-  Connect 2 containers using bridge network
-  Ping from one container to another container

## Instruction

Create bridge network

```sh
$ docker network create -d bridge my-bridge-network
```

Run 2 containers

```sh
# Run 1st container
$ docker run -itd --name container1 --network my-bridge-network alpine
# Run 2nd container
$ docker run -itd --name container2 --network my-bridge-network alpine
```

Check IP address of 2 containers

````sh
$ docker inspect my-bridge-network

[
  {
    ...
    "Containers": {
      "0fcc783330b4930f8b5ac166bf7ea94ecc425f1cac69e5fd143a46bef2a65ad6": {
        "Name": "container2",
        "EndpointID": "6758c568cc6391be6f453db217f7e884bc3863196b42ebc05f48307822fb0692",
        "MacAddress": "02:42:ac:13:00:02",
        "IPv4Address": "172.19.0.2/16",
        "IPv6Address": ""
    },
      "a64d18efb2e3b99890b8db36b7ab25da294e3e1b5f17e24ffbf7a22104015cce": {
        "Name": "container1",
        "EndpointID": "064d14394164d41211dc27722f30c9de91895cfcf3a9927bd1d4bebdb4f21569",
        "MacAddress": "02:42:ac:13:00:03",
        "IPv4Address": "172.19.0.3/16",
        "IPv6Address": ""
    }
    },
    ...
  }
]

Ping from container1 to container2

```sh
$ docker attach container1
/# ping 172.19.0.2

PING 172.19.0.2 (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.271 ms
64 bytes from 172.19.0.2: seq=1 ttl=64 time=0.188 ms
64 bytes from 172.19.0.2: seq=2 ttl=64 time=0.186 ms
...
````

---

> **Note:** See detail at
>
> [lab11_EXPOSE](hhttps://dockerlabs.collabnix.com/beginners/dockerfile/Lab%2311_EXPOSE_instruction.html)

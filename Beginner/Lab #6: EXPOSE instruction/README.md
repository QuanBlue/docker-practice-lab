# Lab #6: EXPOSE instruction

## Introduction

The `EXPOSE` instruction expose a port, the protocol can be UDP or TCP associated with the indicated port, and the default protocol is `TCP` if the protocol is not specified. The EXPOSE won’t be able to map the ports on the host machine. Regardless of the EXPOSE settings, `EXPOSE` port can be override using -p flag while starting the container.

```dockerfile
# EXPOSE instruction
EXPOSE  <port>[/<protocol>]
```

## Instruction

Building Docker image

```sh
$ docker build -t expose:v1 .
```

Create a container based on expose:v1 image

```sh
$ docker container run -d --name expose-inst expose:v1
```

Inspecting the EXPOSE port in the image

```sh
$ docker image inspect --format= expose:v1
```

Publish all exposed ports

-  We can publish all EXPOSE port using -P flag.

```sh
$ docker container run --rm -P -d --name expose-inst-Publish expose:v1
```

-  Checking the publish port

```sh
$ docker container ls

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
24983e09bd86 expose:v1 "nginx -g 'daemon of…" 46 seconds ago Up 45 seconds 0.0.0.0:32768->80/tcp, 0.0.0.0:32768->80/udp expose-inst-Publish
```

---

> **Note:** See detail at
>
> [lab11_EXPOSE](hhttps://dockerlabs.collabnix.com/beginners/dockerfile/Lab%2311_EXPOSE_instruction.html)

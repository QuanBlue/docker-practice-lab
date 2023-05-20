# Lab #2: Create an image with ADD, COPY instruction

## Introduction

COPY and ADD are both Dockerfile instructions that serve similar purposes. They let you copy files from a specific location into a Docker image.

### COPY

`COPY` takes in a src and destination. It only lets you copy in a local file or directory from your host (the machine building the Docker image) into the Docker image itself.

```dockerfile
# Two form of COPY instruction
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"] (this form is required for paths containing
whitespace)
```

### ADD

ADD` lets you do that too, but it also supports 2 other sources. First, you can use a URL instead of a local file / directory. Secondly, you can extract a tar file from the source directly into the destination.

```dockerfile
# ADD instruction
ADD <src> <dest>
```

## Assignment

-  Create an image with ADD instruction
-  Tag your image as labs-add:v1.0
-  Create a container based on that image, and see the extracted tar file.

## Instruction

Add ADD instruction to Dockerfile

```dockerfile
ADD ...
```

Build image
Start container

Enter into Container Shell

```dockerfile
docker attach <CONTAINER_NAME>
```

Checking COPY file

```sh
/ # ls
bin                 media               sbin
dev                 mnt                 srv
etc                 pharosc_8.4.tar.gz  sys
hello.txt           proc                tmp
home                root                usr
lib                 run                 var

```

`hello.txt` is copied into container
`phraosc_8.4.tar.gz` is extracted into container

---

> **Note:** See detail at
>
> -  [Lab-2-Create-an-image-with-ADD-instruction](https://dockerlabs.collabnix.com/beginners/dockerfile/Lab-2-Create-an-image-with-ADD-instruction.html)
> -  [lab4_dockerfile_copy](https://dockerlabs.collabnix.com//beginners/dockerfile/lab4_dockerfile_copy.html)

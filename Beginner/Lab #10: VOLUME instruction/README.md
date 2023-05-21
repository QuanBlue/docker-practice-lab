# Lab #10: VOLUME instruction

## Introduction

A volume is a persistent storage location that exists outside of the container. Volumes are useful for storing data that needs to persist even if the container is stopped or removed.

In a Dockerfile, the `VOLUME` instruction is used to specify a mount point for a volume within the container. The volume will be created when the container is built, and it can be accessed and modified by processes running inside the container.

```dockerfile
# VOLUME instruction
VOLUME <mount_point>
```

## Instruction

Building Docker image

```sh
docker build -t volume:v1 .
```

Create a container based on volume:v1 image

```sh
docker run -d --name volume-test volume:v1
```

Finding the volume created on the host

-  Checking the volume name of the container

   ```sh
   $ docker inspect -f '' volume-test

   ed09456a448934218f03acbdaa31f465ebbb92e0d45e8284527a2c538cc6b016
   ```

-  List out Volume in the host

   ```sh
   $ docker volume ls

   DRIVER              VOLUME NAME
   local               ed09456a448934218f03acbdaa31f465ebbb92e0d45e8284527a2c538cc6b016
   ```

   You will see the volume has been created.

-  Volume mount path in host

   ```sh
   $ docker inspect -f '' volume-test /var/lib/docker/volumes/ed09456a448934218f03acbdaa31f465ebbb92e0d45e8284527a2c538cc6b016/_data
   ```

-  Testing mount working as exepected

   -  Create a file in this folder

      ```sh
      $ touch /var/lib/docker/volumes/ed09456a448934218f03acbdaa31f465ebbb92e0d45e8284527a2c538cc6b016/_data/mytestfile.txt
      ```

   -  Checking file is there in run container
      ```sh
      $ docker exec -it volume-test ls myvol
      ```

---

> **Note:** See detail at
>
> [lab10_VOLUME](https://dockerlabs.collabnix.com/beginners/dockerfile/Lab%2310_VOLUME_instruction.html)

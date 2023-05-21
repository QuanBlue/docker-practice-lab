# Lab #4: Create an image with WORKDIR, USER, RUN, CMD, ENTRYPOINT instruction

## Introduction

### WORKDIR

`WORKDIR` directive in Dockerfile defines the working directory for the rest of the instructions in the Dockerfile. The WORKDIR instruction wont create a new layer in the image but will add metadata to the image config. If the WORKDIR doesn’t exist, it will be created even if it’s not used in any subsequent Dockerfile instruction. you can have multiple WORKDIR in same Dockerfile. If a relative path is provided, it will be relative to the previous WORKDIR instruction.

```dockerfile
# WORKDIR instruction
WORKDIR </path/to/workdir>
```

### USER

The `USER` directive is similar to `WORKDIR`, which changes the state of the environment and affects future layers. `WORKDIR` is to change the working directory, and `USER` is the identity of the commands such as `RUN`, `CMD` and `ENTRYPOINT`.

```dockerfile
# USER instruction
USER <username>
```

### RUN, CMD, ENTRYPOINT

`RUN` executed command line during build in a new layer._Commonly used to install software packages and libraries._

```dockerfile
# RUN instruction
RUN <command>
```

`CMD` executes default command when we initialize container from image (docker run) _Commonly put in the end of the Dockerfile._

> This default command can be overridden from command line when initializing container.

```dockerfile
# CMD instruction
# exec form, this is the preferred form
CMD ["<executable>","<param1>","<param2>"]

# as default parameters to ENTRYPOINT
CMD ["<param1>","<param2>"]

# shell form
CMD <command> <param1> <param2>
```

`ENTRYPOINT` is quite similar to CMD which is used when initializing the container, but ENTRYPOINT cannot be overwritten from the command line when initializing the container.

```dockerfile
# ENTRYPOINT can be configured in two forms:
#Exec Form
ENTRYPOINT [“<executable>”, “<param1>”, “<param2>”]

#Shell Form
ENTRYPOINT <command> <param1> <param2>
```

> **CMD and ENTRYPOINT Use case:**
>
> -  **Prefer `ENTRYPOINT` to `CMD` when building executable Docker images** and you need a command always to be executed. Additionally, use `CMD` if you need to provide extra default arguments that could be overwritten from the command line when the docker container runs. It is not necessary to use both the commands together, try to find your own use case while building the Dockerfile and select the appropriate command.
> -  **Only one `CMD` and `ENTRYPOINT` instruction is allowed in a Dockerfile**. If you list more than one `CMD` and `ENTRYPOINT` then only the last `CMD` and `ENTRYPOINT` will take effect.

> **Note:** can not run with flag `-itd` if Dockerfile have `CMD` and `ENTRYPOINT` instruction

## Assignment

-  Create an image with WORKDIR, RUN, CMD, ENTRYPOINT instruction
-  Tag your image as labs-add:v1.0
-  Create a container based on that image, and see the extracted tar file.

## Instruction

Build image

```sh
docker build -t lab4:v1 .
```

Run container

```sh
docker run lab4:v1
```

---

> **Note:** See detail at
>
> -  [lab4_CMD](https://dockerlabs.collabnix.com//beginners/dockerfile/lab4_cmd.html)
> -  [lad5_ENTRYPOINT](https://dockerlabs.collabnix.com/beginners/dockerfile/Dockerfile-ENTRYPOINT.html)
> -  [lab6_WORKDIR](https://dockerlabs.collabnix.com/beginners/dockerfile/WORKDIR_instruction.html)
> -  [lab7_RUN](https://dockerlabs.collabnix.com/beginners/dockerfile/Lab%237_RUN_instruction.html)
> -  [cmd-and-entrypoint-differences](https://devtron.ai/blog/cmd-and-entrypoint-differences/#:~:text=CMD%3A%20Sets%20default%20parameters%20that,Docker%20containers%20with%20CLI%20parameters.)

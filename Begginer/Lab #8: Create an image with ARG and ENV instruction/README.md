# Lab #8: Create an image with ARG, ENV instruction

## Introduction

### ARG

The `ARG` directive in Dockerfile defines the parameter name and defines its default value. This default value can be overridden by the --build-arg <parameter name>=<value> in the build command docker build.

```dockerfile
ARG <parameter name>[=<default>]
```

### ENV

The `ENV` instruction in Dockerfile sets the environment variable for your container when you start. The default value can be overridden by passing `--env <key>=<value>` when you start the container.

```dockerfile
ENV <parameter name>[=<default>]
```

> **Note:** > `AGR`: only available during docker image build
> `ENV`: start to appear and be available from the command line where they are declared

## Instruction

Building Docker Image

-  With default argument

   ```sh
   docker build -t lab8:v1 .
   ```

-  Passing the `argument` during image build time

   ```sh
   docker build -t lab8:v1 --build-arg WELCOME_USER=Docker .
   ```

Running container

-  Normal

   ```sh
   docker run lab8:v1
   ```

-  Override existing `env` while running container
   ```sh
   docker run --env WELCOME_MESSAGE="[ENV] Welcome to Docker Workshop" lab8:v1
   ```

---

> **Note:** See detail at
>
> -  [lab8_ARG](https://dockerlabs.collabnix.com//beginners/dockerfile/arg.html)
> -  [lad9_ENV](https://dockerlabs.collabnix.com/beginners/dockerfile/Lab_%239_ENV_instruction.html)

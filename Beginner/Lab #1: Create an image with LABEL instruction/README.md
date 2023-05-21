# Lab #1: Create an image with LABEL instruction

## Introduction

**LABEL:** You can add `labels` to your image to help organize images by project, record licensing information, to aid in automation, or for other reasons. For each label, add a line beginning with `LABEL` and with one or more **key-value** pairs. The following examples show the different acceptable formats.

Docker offers support to add labels into images as a way to add custom metadata on them.

```dockerfile
# The label syntax on your Dockerfile is as follows:
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

> **Note:** An image can have more than one label. You can specify multiple labels on a single line.

## Instruction

Creating Dockerfile

```dockerfile
FROM ...
RUN ...
...
```

> **Note:** you can consult `./Dockerfile`

Build Docker Image

```sh
docker build -t <NAME:TAG> <PATH>
```

> `-t`: tag
> `PATH`: Path contain Dockerfile to build image

Tagging image with new tag

```sh
docker tag <SOURCE_IMAGE[:TAG]> <TARGET_IMAGE[:TAG]>
```

Verify the Images

```sh
docker images
```

Create container from new created image

```sh
docker run -itd --name <NAME_CONTAINER> <IMAGE>
```

> `-itd`:
>
> -  `-it`: launch containers interactively.
> -  `-d`: to start them in background.
>    `NAME_CONTAINER`: your custom name the container
>    `IMAGE`: the Image you just created with Dockerfile

Enter into Container Shell

```sh
docker attach <CONTAINER>
```

> `CONTAINER`: the name of container you just created

Control the container

---

> **Note:** See detail at
>
> -  [lab1_dockerfile_git](https://dockerlabs.collabnix.com/beginners/dockerfile/lab1_dockerfile_git.html)
> -  [lab12_label_git](https://dockerlabs.collabnix.com/beginners/dockerfile/Label_instruction.html)

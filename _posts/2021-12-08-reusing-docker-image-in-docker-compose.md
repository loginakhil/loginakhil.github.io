---
layout: post
title: "Re-using docker image in docker compose"
tags: [docker, docker-compose, build, image]
date: 2021-12-08
comments: false
---



It’s very common in a docker-compose file that multiple services use the same docker build.
See the below example :


```yml
version: "3.9"
   
services:
  app_a:
    build:
      context: .
      dockerfile: Dockerfile
    command: "http-server"
  app_b:
    build:
      context: .
      dockerfile: Dockerfile
    command: "event-listener"

```

Here you can see that both `app_a` and `app_b` are using the same docker build.

👎 This doesn’t look good because it duplicates the build process and also increases the build time.

We make this a bit elegant by adding a image name as follows:


```yml
version: "3.9"
   
services:
  app_a:
    build:
      image: baseimage
      context: .
      dockerfile: Dockerfile
    command: "http-server"
  app_b:
    image: baseimage
    command: "event-listener"
    depends:
      - app_a

```

👍 This approach will help us save some time and avoid duplicate build instructions :

Read more about this in the official documentation [https://docs.docker.com/compose/compose-file/compose-file-v3/#build](https://docs.docker.com/compose/compose-file/compose-file-v3/#build)

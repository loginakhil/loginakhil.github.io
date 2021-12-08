---
layout: post
title: "Using templates in docker compose"
tags: [docker, docker-compose, templating, yaml]
date: 2021-12-05
comments: false
---



In the below example, for both `db` and `web` services, we have duplicated the `environment` configuration.

```yml
version: "3.9"
   
services:
  db:
    image: postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    depends_on:
      - db

```

Have you ever wondered how to re-use configuration fragments in docker-compose files?


From docker compose version `3.4` onwards, we can achieve this using **extension-fields**



This can be used in two ways

- [Using YAML anchors](https://yaml.org/spec/1.2-old/spec.html#id2765878)


```yml
version: "3.9"

x-env-config:
    &default-env-config
    - POSTGRES_NAME=postgres
    - POSTGRES_USER=postgres
    - POSTGRES_PASSWORD=postgres

services:
    db:
        image: postgres
        volumes:
            - ./data/db:/var/lib/postgresql/data
        environment: *default-env-config
    web:
        build: .
        command: python manage.py runserver 0.0.0.0:8000
        volumes:
            - .:/code
        ports:
            - "8000:8000"
        environment: *default-env-config
        depends_on:
            - db
```


- [Using YAML merge type](https://yaml.org/type/merge.html)


```yml
version: "3.9"

x-env-config: &default-env-config
    environment:    
        - POSTGRES_NAME=postgres
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=postgres
services:
    db:
        image: postgres
        volumes:
          - ./data/db:/var/lib/postgresql/data
        <<: *default-env-config
    web:
        build: .
        command: python manage.py runserver 0.0.0.0:8000
        volumes:
            - .:/code
        ports:
            - "8000:8000"
        <<: *default-env-config
        depends_on:
            - db
```


Read more about this in the official documentation [https://docs.docker.com/compose/compose-file/compose-file-v3/#extension-fields](https://docs.docker.com/compose/compose-file/compose-file-v3/#extension-fields)

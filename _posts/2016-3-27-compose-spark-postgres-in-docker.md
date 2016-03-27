---
layout: post
title: Compose Spark and Postgres in Docker
categories: docker,spark,SQL
description: docker-compose , the new tool for lessing pains of managing many containers, giving you cleaner, more reusable code, and more fun. It is a tool for defining and running multi-container Docker applications.
comments: true
tags: [docker, spark]
image:
feature:
credit:
creditlink:
date: 2016-3-27T00:00:00+00:00
author: superching
---

# Compose Spark and Postgres in Docker

Do you have experience that you want to set an environments just for development or testing. Then having a bunch of services stuffed into one container, even you known that is "anti-pattern". You do it just because you foresee the difficulty of connected and running multiple containers together.

### Docker-compose

Now Docker has docker-compose , the new tool for lessing pains of managing many containers, giving you cleaner, more reusable code, and more fun. It is a tool for defining and running multi-container Docker applications. You use a file `docker-compose.yaml` to make up your app by configuring all your application’s services— for instances the databases, queues, web service, etc and how they are build, started, depended on, etc. Then, in a single command, you create and start all the service containers from your configuration.

### Write a Docker-compose file

I have an [application](https://github.com/superChing/Spark_Postgres_Example) that uses Slick to import [Movielens](http://grouplens.org/datasets/movielens/) data into Postgres SQL and Spark for computing movie ranks. They're wired by docker-compose. I will give this as an example how docker-compose file being written. Before you look the file, you need to know some basic docker-compose file options:

- First specify a service name, it is a container with the following options.
- build: options that are applied at build time. Usually a path to your directory containing Dockerfile and building environment.
- links: Link to container of other service. Just specify the service name you defined in docker-compose file.
- expose: Expose ports without publishing them to the host. This is important in that you need to know what ports on which this service provide services, or your linked services will not work normally.
- extend: specify the service in another docker-compose file, it enables reuse of common configurations among different docker-compose files.
- restart: restart policy once any failure occurs. It's the same option as in docker run command.
- volumns: same as Dockerfile. mount the specified path to container.
- ports: same as Dockerfile. Expose ports and port mapping between host and container.

For more other options, please refer to compose file reference (https://docs.docker.com/compose/compose-file/). Actually they are much the same with dockerfile or the options from docker run command, with some new options working in higher level.

A `docker-compose.yaml` file for this application would looks like this:
```
myapp:
  build: .
  links:
    - master
    - postgres

master:
  image: gettyimages/spark
  command: /usr/spark/bin/spark-class org.apache.spark.deploy.master.Master -h master
  hostname: master
  environment:
    SPARK_CONF_DIR: /conf
    SPARK_MASTER_IP: master
    SPARK_MASTER_PORT: 7077
  expose:
    - 7001
    - 7077
    - 6066
  ports:
    - 7077:7077    
  volumes:
    - ./conf/master:/conf
    - .:/code
  links:
    - postgres

worker:
  image: gettyimages/spark
  command: /usr/spark/bin/spark-class org.apache.spark.deploy.worker.Worker spark://master:7077
  hostname: worker
  environment:
    SPARK_CONF_DIR: /conf
  volumes:
    - ./conf/worker:/conf
  links:
    - master
    - postgres
  restart: on-failure

postgres:
  image: postgres:9.4
  ports:
    - 5432:5432
  environment:
    POSTGRES_USER: dev
    POSTGRES_PASSWORD: dev
    POSTGRES_DB: dev
```

Once the `docker-compose.yaml` is set, you can type `docker-compose up -d` to launch your services and the Docker will do all the wiring works for you. You can also enter the respective service from docker or from `docker-compose docker-compose run myapp bash`.
Moreover you can scale your Spark services with `docker-compose scale worker=3` to fit your current loading.

The workable example is on [Github](https://github.com/superChing/Spark_Postgres_Example), checking it out and have fun with more extension as you would like !

#### references:
Official Docker-compose document: https://docs.docker.com/compose
The spark image is based on : https://github.com/gettyimages/docker-spark  

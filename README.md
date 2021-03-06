[![CircleCI](https://circleci.com/gh/bitnami/bitnami-docker-memcached/tree/master.svg?style=shield)](https://circleci.com/gh/bitnami/bitnami-docker-memcached/tree/master)
[![Slack](https://img.shields.io/badge/slack-join%20chat%20%E2%86%92-e01563.svg)](http://slack.oss.bitnami.com)

# What is Memcached?

> Memcached is an in-memory key-value store for small chunks of arbitrary data (strings, objects) from results of database calls, API calls, or page rendering.

[memcached.org](http://memcached.org/)

# TL;DR;

```bash
docker run --name memcached bitnami/memcached:latest
```

## Docker Compose

```bash
$ curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-memcached/master/docker-compose.yml > docker-compose.yml
$ docker-compose up -d
```

# Why use Bitnami Images?

* Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
* With Bitnami images the latest bug fixes and features are available as soon as possible.
* Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
* Bitnami images are built on CircleCI and automatically pushed to the Docker Hub.
* All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading linux distribution.

# Get this image

The recommended way to get the Bitnami Memcached Docker Image is to pull the prebuilt image from the [Docker Hub Registry](https://hub.docker.com).

```bash
docker pull bitnami/memcached:latest
```

To use a specific version, you can pull a versioned tag. You can view the [list of available versions](https://hub.docker.com/r/bitnami/memcached/tags/) in the Docker Hub Registry.

```bash
docker pull bitnami/memcached:[TAG]
```

If you wish, you can also build the image yourself.

```bash
docker build -t bitnami/memcached:latest https://github.com/bitnami/bitnami-docker-memcached.git
```

# Connecting to other containers

Using [Docker container networking](https://docs.docker.com/engine/userguide/networking/), a Memcached server running inside a container can easily be accessed by your application containers.

Containers attached to the same network can communicate with each other using the container name as the hostname.

## Using the Command Line

### Step 1: Create a network

```bash
$ docker network create app-tier --driver bridge
```

### Step 2: Launch the Memcached server instance

Use the `--network app-tier` argument to the `docker run` command to attach the Memcached container to the `app-tier` network.

```bash
$ docker run -d --name memcached-server \
    --network app-tier \
    bitnami/memcached:latest
```

### Step 3: Launch your application container

```bash
$ docker run -d --name myapp \
    --network app-tier \
    YOUR_APPLICATION_IMAGE
```

> **IMPORTANT**:
>
> 1. Please update the **YOUR_APPLICATION_IMAGE_** placeholder in the above snippet with your application image
> 2. In your application container, use the hostname `memcached-server` to connect to the Memcached server

## Using Docker Compose

When not specified, Docker Compose automatically sets up a new network and attaches all deployed services to that network. However, we will explicitly define a new `bridge` network named `app-tier`. In this example we assume that you want to connect to the Memcached server from your own custom application image which is identified in the following snippet by the service name `myapp`.

```yaml
version: '2'

networks:
  app-tier:
    driver: bridge

services:
  memcached:
    image: 'bitnami/memcached:latest'
    networks:
      - app-tier
  myapp:
    image: 'YOUR_APPLICATION_IMAGE'
    networks:
      - app-tier
```

> **IMPORTANT**:
>
> 1. Please update the **YOUR_APPLICATION_IMAGE_** placeholder in the above snippet with your application image
> 2. In your application container, use the hostname `memcached` to connect to the Memcached server

Launch the containers using:

```bash
$ docker-compose up -d
```

# Configuration

## Specify the cache size

By default, the Bitnami Memcached container starts with a cache size of 64mb. You can specify other value using the `MEMCACHED_CACHE_SIZE` environment variable.

```bash
docker run --name memcached -e MEMCACHED_CACHE_SIZE=128 bitnami/memcached:latest
```

or using Docker Compose:

```yaml
version: '2'

services:
  memcached:
    image: 'bitnami/memcached:latest'
    ports:
      - '11211:11211'
    environment:
      - MEMCACHED_CACHE_SIZE=128
```

> The default value of the `MEMCACHED_CACHE_SIZE` is `64`.


## Creating the Memcached admin user

Authentication on the Memcached server is disabled by default. To enable authentication, specify a username and password for the Memcached admin user using the `MEMCACHED_USERNAME` and `MEMCACHED_PASSWORD` environment variables.

```bash
docker run --name memcached \
  -e MEMCACHED_USERNAME=my_user \
  -e MEMCACHED_PASSWORD=my_password \
  bitnami/memcached:latest
```

or using Docker Compose:

```yaml
version: '2'

services:
  memcached:
    image: 'bitnami/memcached:latest'
    ports:
      - '11211:11211'
    environment:
      - MEMCACHED_USERNAME=my_user
      - MEMCACHED_PASSWORD=my_password
```

> The default value of the `MEMCACHED_USERNAME` is `root`.

# Logging

The Bitnami Memcached Docker image sends the container logs to the `stdout`. To view the logs:

```bash
docker logs memcached
```

or using Docker Compose:

```bash
docker-compose logs memcached
```

You can configure the containers [logging driver](https://docs.docker.com/engine/admin/logging/overview/) using the `--log-driver` option if you wish to consume the container logs differently. In the default configuration docker uses the `json-file` driver.

# Maintenance

## Upgrade this image

Bitnami provides up-to-date versions of Memcached, including security patches, soon after they are made upstream. We recommend that you follow these steps to upgrade your container.

### Step 1: Get the updated image

```bash
docker pull bitnami/memcached:latest
```

or if you're using Docker Compose, update the value of the image property to
`bitnami/memcached:latest`.

### Step 2: Remove the currently running container

```bash
docker rm -v memcached
```

or using Docker Compose:

```bash
docker-compose rm -v memcached
```

### Step 3: Run the new image

Re-create your container from the new image.

```bash
docker run --name memcached bitnami/memcached:latest
```

or using Docker Compose:

```bash
docker-compose start memcached
```

# Notable Changes

## 1.5.0-r1

- The memcached container has been migrated to a non-root container approach. Previously the container run as `root` user and the memcached daemon was started as `memcached` user. From now own, both the container and the memcached daemon run as user `1001`.
  As a consequence, the configuration files are writable by the user running the memcached process.

## 1.4.25-r4

- `MEMCACHED_USER` parameter has been renamed to `MEMCACHED_USERNAME`.

## 1.4.25-r0

- The logs are always sent to the `stdout` and are no longer collected in the volume.

# Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/bitnami-docker-memcached/issues), or submit a [pull request](https://github.com/bitnami/bitnami-docker-memcached/pulls) with your contribution.

# Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/bitnami-docker-memcached/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container (`echo $BITNAMI_IMAGE_VERSION` inside the container)
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

# Community

Most real time communication happens in the `#containers` channel at [bitnami-oss.slack.com](http://bitnami-oss.slack.com); you can sign up at [slack.oss.bitnami.com](http://slack.oss.bitnami.com).

Discussions are archived at [bitnami-oss.slackarchive.io](https://bitnami-oss.slackarchive.io).

# License

Copyright (c) 2015-2018 Bitnami

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

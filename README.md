# peerrails/nginx-rtmp

- [Introduction](#introduction)
  - [Contributing](#contributing)
  - [Issues](#issues)
- [Getting started](#getting-started)
  - [Installation](#installation)
  - [Quickstart](#quickstart)
  - [Command-line arguments](#command-line-arguments)
  - [Configuration](#configuration)
  - [Logs](#logs)
- [Maintenance](#maintenance)
  - [Upgrading](#upgrading)
  - [Shell Access](#shell-access)

# Introduction

`Dockerfile` to create a [Docker](https://www.docker.com/) container image for [NGINX](http://nginx.org/en/) with [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) module support.

NGINX is a web server with a strong focus on high concurrency, performance and low memory usage. It can also act as a reverse proxy server for HTTP, HTTPS, SMTP, POP3, and IMAP protocols, as well as a load balancer and an HTTP cache.

# Getting started

## Installation

This image is available as a [trusted build](//hub.docker.com/r/peerrails/nginx) on the [Docker hub](//hub.docker.com) and is the recommended method of installation.

```bash
docker pull peerrails/nginx:1.8.0-5
```

Alternatively you can build the image yourself.

```bash
git clone https://github.com/peerrails/docker-nginx.git
cd docker-nginx
docker build --tag $USER/nginx .
```

## Quickstart

Start NGINX using:

```bash
docker run --name nginx -d --restart=always \
  --publish 1935:1935 \
  peerrails/nginx:1.8.0-5
```

*Alternatively, you can use the sample [docker-compose.yml](docker-compose.yml) file to start the container using [Docker Compose](https://docs.docker.com/compose/)*

## Command-line arguments

You can customize the launch command of NGINX server by specifying arguments to `nginx` on the `docker run` command. For example the following command prints the help menu of `nginx` command:

```bash
docker run --name nginx -it --rm \
  --publish 80:80 \
  peerrails/nginx:1.8.0-5 -h
```

## Configuration

To configure NGINX as per your requirements edit the default [nginx.conf](nginx.conf) and mount it at `/etc/nginx/nginx.conf`.

```bash
docker run --name nginx -it --rm \
  --publish 80:80 \
  --volume /srv/docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
  peerrails/nginx:1.8.0-5
```

To configure virtual hosts, mount the directory containing the virtual host configurations at `/etc/nginx/sites-enabled/`.

```bash
docker run --name nginx -it --rm \
  --publish 80:80 \
  --volume /srv/docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
  --volume /srv/docker/nginx/sites-enabled:/etc/nginx/sites-enabled \
  peerrails/nginx:1.8.0-5
```

> **Note**: SELinux users should update the security context of the host mountpoints so that it plays nicely with Docker:
>
> ```bash
> mkdir -p /srv/docker/nginx
> chcon -Rt svirt_sandbox_file_t /srv/docker/nginx
> ```

To reload the NGINX configuration on a running instance you can send the `HUP` signal to the container.

```bash
docker kill -s HUP nginx
```

## Logs

To access the NGINX logs, located at `/var/log/nginx`, you can use `docker exec`. For example, if you want to tail the logs:

```bash
docker exec -it nginx tail -f /var/log/nginx/access.log
```

# Maintenance

## Upgrading

To upgrade to newer releases:

  1. Download the updated Docker image:

  ```bash
  docker pull peerrails/nginx:1.8.0-5
  ```

  2. Stop the currently running image:

  ```bash
  docker stop nginx
  ```

  3. Remove the stopped container

  ```bash
  docker rm -v nginx
  ```

  4. Start the updated image

  ```bash
  docker run -name nginx -d \
    [OPTIONS] \
    peerrails/nginx:1.8.0-5
  ```

## Shell Access

For debugging and maintenance purposes you may want access the containers shell. If you are using Docker version `1.3.0` or higher you can access a running containers shell by starting `bash` using `docker exec`:

```bash
docker exec -it nginx bash
```

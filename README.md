# Startup Services Using Docker Compose

Provides an easy-to-extend stack of services via Docker Compose.

## Requirements

- A Linux host machine with Docker and Docker Compose installed.
- a local user (such as `torgo`), as well as their UID (such as `1000`), and a local group (such as `valleylodge`), as well as its GID (such as `7000`). The user's primary group should be `valleylodge` and they should also be a member of the `docker` group.
- a number of custom variables to make this "yours"

## Shared Variables

- `PROJECT_NAME`: Overall name of the project (or "stack", as it is called in Portainer). **Default:** `startup`
- `DOCKER_NETWORK_IPV4_CIDR`: The IPv4 address of the Docker network we will use for our default "bridge" network, shared by most of our containers. **Default:** `172.18.0.0/24`
- `DOCKER_NETWORK_IPV4_GATEWAY`: The IPv4 address of the gateway on the shared Docker network. **Default:** `172.18.0.1`
- `HOST_COMPOSE_FOLDER`: The folder in which the composed service and vars files reside. It can be an absolute or relative path. **Default:** `./compose.d`
- `HOST_DATA_FOLDER`: The folder or mount in which stateful data lives. See below for more info. **Default:** `/var/local`
- `HOST_GROUP_NAME`: The primary group name, used for the default network name. **Default:** `valleylodge`
- `HOST_IPV4_LOCAL`: The IPv4 address of the host machine on the local network. **Default:** 192.168.1.2

## `HOST_DATA_FOLDER`

This folder is where stateful data lives for your services. Each service goes in an individually-named folder in `apps`, inside each of which reside sub-folders such as `config` or `data` for configuration and data, respectively. Several service files also refer to a `secrets` folder, which is where an encrypted filesystem containing sensitive data (such as password files) should be mounted.

## Main Files

- `docker-compose.yml`: Primary compose file. Provides overall project name, shared network and services.
- `docker-compose.env`: Provides variables shared by multiple containers.

## Extending with Services

Service definitions are kept in `HOST_COMPOSE_FOLDER` and loaded individually through the use of Docker Compose's `include` directive. See `docker-compose.yml` for an example of how to include services.

### Services available:

- Traefik reverse proxy

## Usage

To bring the stack up once at a terminal prompt, type `docker compose --env-file /path/to/docker-compose.env --file /path/to/docker-compose.yml up -d` and press **Enter**.

This repo also comes with a [`systemd.service(5)` unit file](https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html) that can safely bring the stack up and down when the system is started, shut down or rebooted. Edit the `.service` unit file (point `ExecStart=` and `ExecStop=` to your Docker Compose files and make sure `User=` and `Group=` are set correctly), put the unit file in `/etc/systemd/system`, reload the daemon with `systemctl daemon-reload`, and start it with `systemctl enable --now torgo-startup-docker-compose.service`. Now, services described in `docker-compose.yml` should be safely brought up and down when the machine starts, shuts down, or reboots.

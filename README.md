# EdgeX Foundry Lab Setup

## Overview

This folder contains docker-compose.yml for EdgeX Foundry Fuji release (1.1.0) in home lab.

Originally [`docker-compose-fuji-no-secty.yml`](https://github.com/edgexfoundry/developer-scripts/blob/master/releases/fuji/compose-files/docker-compose-fuji-no-secty.yml) from [`edgexfoundry/developer-scripts`](https://github.com/edgexfoundry/developer-scripts).

## System Requirements

* Ubuntu 19.10

## Setup

### Install Docker CE (Community Edition)

The procedure described in [official guide](https://docs.docker.com/install/linux/docker-ce/ubuntu/) does not work correctly.
Please note that official docker-ce packages for 19.10 (`eoan`) does **NOT RELEASED YET**.
Currently we have to use packages for 19.04 (`disco`).

```sh
$ sudo apt update
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu disco stable"
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io
$ sudo usermod -aG docker ${USER}
```

### Install Docker Compose

```sh
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

### Pull the EdgeX Foundry docker images

```sh
$ sudo docker-compose pull
```

### Start EdgeX Foundry

```sh
$ sudo docker-compose up -d
$ sudo docker-compose ps
```

### Stop EdgeX Foundry

```sh
$ sudo docker-compose stop
```

## Cleanup

To remove all docker images related to EdgeX Foundry, run following command.
This command does not remove volumes.

```sh
$ sudo docker-compose down --rmi all
```
To remove volumes, run `sudo docker volume ls` and `sudo docker volume rm <volume_name>`.
If you want to remove all unused volumes, you can use this:

```sh
$ sudo docker volume prune
```


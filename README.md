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
$ docker-compose pull
```

### Start EdgeX Foundry

```sh
$ docker-compose up -d
$ docker-compose ps
```

Please note `device-mqtt-go` has exited because configuration file for this device service includes incorrect values for your future setup.

If you want to make this work correctly, follow (Setup with MQTT Device Service)[#setup-with-mqtt-device-service] section.

### Stop EdgeX Foundry

```sh
$ docker-compose stop
```

Or, if you want to remove containers, try this:

```sh
$ docker-compose down
```

## Cleanup

To remove all docker images related to EdgeX Foundry, run following command.

This command does not remove volumes.

```sh
$ docker-compose down --rmi all
```

To remove volumes, run `sudo docker volume ls` and `sudo docker volume rm <volume_name>`.
If you want to remove all unused volumes (including used outside of EdgeX Foundry), you can use this:

```sh
$ docker volume prune
```

## Setup with MQTT Device Service

This repository includes files to try walkthrough [MQTT Example and Tutorial in official document](https://docs.edgexfoundry.org/Ch-ExamplesAddingMQTTDevice.html).

MQTT device service has already included in `docker-compose.yml`, but not work correctly by default.

If you make this work, you have to do followings.

### Run MQTT broker

You can run MQTT broker using container. 

```sh
$ docker run -d --rm --name broker -p 1883:1883 eclipse-mosquitto
```

### Run MQTT device simulator

This example device will work with MQTT topics called `DataTopic`, `CommandTopic`, and `ResponseTopic`.

You can simulate this device by using mqtt-scripts by following command. You should replace `<your-broker-ipaddress>` with your broker IP address.

```sh
$ cd mqtt-scripts
$ docker run -d --restart=always --name=mqtt-scripts -v $PWD:/scripts dersimn/mqtt-scripts --url mqtt://<your-broker-ipaddress> --dir /scripts
```

Now you have MQTT broker and publisher. You can see the values sent from this simulator every 15 seconds by subscribing `DataTopic`.

```sh
$ mosquitto_sub -h <your-broker-ipaddress> -t DataTopic
{"name":"MQ_DEVICE","cmd":"randnum","randnum":"26.1"}
{"name":"MQ_DEVICE","cmd":"randnum","randnum":"27.9"}
{"name":"MQ_DEVICE","cmd":"randnum","randnum":"25.0"}
...
```

### Modify TOML file and then run EdgeX Foundry

Finally modify `mqtt/configuration.toml` file to make Device Service subscribe broker correctly.

Replace `<your-broker-ipaddress>` in this file with your broker IP address.

Now you can start EdgeX Foundry.

```sh
$ docker-compose down
$ docker-compose up -d
$ docker-compose ps
```

See [official tutorial](https://docs.edgexfoundry.org/Ch-ExamplesAddingMQTTDevice.html#find-executable-commands) or Web GUI (`http://<your-ipaddress>:8080/`) to check if it works correctly.
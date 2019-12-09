+++
author = "Esteban"
date = 2017-06-01T08:07:56Z
description = ""
draft = false
slug = "easy-way-to-log-network-traffic-from-docker-containers"
title = "Easy way to log network traffic from docker containers"

+++


# Introduction
Let's say that you are working on a project using **docker** and running several containers in the host network, or you have multiple containers leveraging the power of the **docker-compose** script and running concurrently sharing a single docker network. Now for instance, imagine that you experience some slowness while they are executing, and you suspect one or several of them are misusing the network causing a performance bottleneck, and you want to identify it.  Another case could be a scenario where you want to extract network traffic's statistics and plot them afterwards using **tcptrace** and **gnuplot**.

It would be handy to have a tool that automated the process of detecting new containers added to a given docker network, start watching their traffic and dump it into *.pcap* files following a structured format.

I wrote the *docker-network* script to provide this piece of functionality. You can [get the script here](https://github.com/fr0gs/docker-watcher).

# How

You just need to clone the script and execute it once you have started your containers.

```sh
$ ./docker-watcher.sh -d|--discovery <discovery_time> -t|--t <time_period> -r|--rotate <yes/no> -n|--network <network> (default: all) -o|--output <output_folder>
```

* *discovery_period*: is the interval time for the script to wait to check if new containers have been started.
* *time_period*: is the elapsed time to store new traffic dumps.
* *rotate*: whether or not to rotate logs
* *network*: the docker network to observe
* *output_folder*: the output folder where pcaps will be stored.

*docker-watcher*'s functioning is very easy. It will watch for new containers added into the specified docker network and start a new **tcpdump** process instructing it to listen into the target docker network's interface and filtering the traffic by container.

For each container one or several *.pcap* dump files will be created, depending on whether or not you want to rotate logs.

Here you can see an example of running a **mysql** docker container and starting to watch traffic for it:

![](/images/docker-watcher-cropped.png)

# Considerations
In order to extract information about the docker containers, the script uses Go templates and heavily depends on the format of the information output when performing **docker inspect** to a container. Changes in the structure of the information would break the tool, needing to adapt it to the new structure.

The latest docker version it supports is:

```sh
Client:
 Version:      17.05.0-ce
 API version:  1.29
 Go version:   go1.7.5
 Git commit:   89658be
 Built:        Thu May  4 22:10:54 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.05.0-ce
 API version:  1.29 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   89658be
 Built:        Thu May  4 22:10:54 2017
 OS/Arch:      linux/amd64
 Experimental: false
```


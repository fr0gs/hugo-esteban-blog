+++
author = "Esteban"
categories = ["bash", "elasticsearch", "kibana", "docker"]
date = 2017-07-13T07:27:26Z
description = "A small script to load automatically json files into ElasticSearch containers and visualize them with Kibana. "
draft = false
slug = "automatically-loading-json-files-to-elasticsearch"
tags = ["bash", "elasticsearch", "kibana", "docker"]
title = "Automatically loading json files to ElasticSearch"

+++


# Introduction

Right now at work I am working in the ([Big Data Europe](https://www.big-data-europe.eu/)) project, a joint effort or several organizations and enterprises accross Europe to create a platform that provides a set of software services that allow to implement big data pipelines, with minimal effort compared to other stacks and in an extremely cost effective way.

This is to make any company or organization that wants to make sense of their data using Big Data an easy starter to play around with the technologies that allow so.

One of the pieces I developed is [mu-bde-logging](https://github.com/big-data-europe/mu-bde-logging), a standalone system that allows to log HTTP traffic from running docker containers and post it into an EL(K) stack in real time for further visualization.

Last requirement was to add the possibility to replay old traffic backups and post them into the **ElasticSearch** instance to visualize them offline in **Kibana**.

So I wrote a small script to scan for the transformed **.ha** files (json format) in a given folder and replay them into the ElasticSearch container.
Since ElasticSearch & Kibana containers are part of a *docker-compose.yml* project, I didn't care much about being generic and used the name the *docker-compose* script will give the containers, but it is easy to change & extend.


# The Code

This is what I came up with:


```sh
#!/usr/bin/env bash

#/ Usage: ./backup_replay.sh <backups_folder>
#/ Description: Run ElasticSearch and Kibana standalone and post every enriched .har file in the backups folder to ElasticSearch.
#/ Examples: ./backup_replay.sh backups/
#/ Options:
#/     --help: Display this help message
usage() { grep '^#/' "$0" | cut -c4- ; exit 0 ; }
expr "$*" : ".*--help" > /dev/null && usage

BACKUP_DIR="../backups/"

# Convenience logging function.
info()    { echo "[INFO]    $@"  ; }

cleanup() {
  true;
}

# Poll the ElasticSearch container
poll() {
  local elasticsearch_ip="$1"
  local result=$(curl -XGET http://${elasticsearch_ip}:9200 -I 2>/dev/null | head -n 1 | awk '{ print $2 }')

  if [[ $result == "200" ]]; then
    return 1 # ElasticSearch is up.
  else
    return 0 # It will execute as long as the return code is zero.
  fi
}

# Parse Parameters
while [ "$#" -gt 1 ];
  do
  key="$1"

  case $key in
      -f|--folder)
      BACKUP_DIR="$2" # EXAMPLE
      shift
      ;;
      --default)
      default=YES
      ;;
    *)
    ;;
  esac
  shift
done


if [[ "${BASH_SOURCE[0]}" = "$0" ]]; then
    trap cleanup EXIT

    # Start Elasticsearch & Kibana with docker Compose
    which docker-compose >/dev/null
    if (( $(echo $?) == "0" )); then
      docker-compose up -d elasticsearch kibana
    else
      info "Install docker-compose!"
      exit -1
    fi

    # Poll ElasticSearch until it is up and we can post hars to it.
    elasticsearch_ip=$(docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mubdelogging_elasticsearch_1)

    info "ElasticSearch container ip: ${elasticsearch_ip}"

    while poll ${elasticsearch_ip}
    do
      info "ElasticSearch is not up yet."
      sleep 2
    done

    # Find all .trans.har files in the specified backups folder/
    # Per each one, pos to ElasticSearch.
    info "Ready to work!"
    info "POST all enriched hars "
    find ${BACKUP_DIR} -name "*.trans.har" | sed 's/^/@/g' | xargs -i /bin/bash -c "sleep 0.5; curl -XPOST 'http://$elasticsearch_ip:9200/hars/har?pretty' --data-binary {}"
fi
```


Have fun!


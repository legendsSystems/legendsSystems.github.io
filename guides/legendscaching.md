# legendsCaching

![Status](https://img.shields.io/badge/status-active-success.svg) [![GitHub Issues](https://img.shields.io/github/issues/legendsSystems/legendsCaching.svg)](https://github.com/legendsSystems/legendsCaching/issues) [![GitHub Pull Requests](https://img.shields.io/github/issues-pr/legendsSystems/legendsCaching.svg)](https://github.com/legendsSystems/legendsCaching/pulls) ![License](https://img.shields.io/badge/license-MIT-blue.svg)

***

## Getting Started <a href="#getting_started" id="getting_started"></a>

These instructions will get you a copy of the project up and running on your VPS for hosting files separate from you game server. It will load the files from your game server and cache them, so they are ready to stream to players.

_**Build script only compatible with ubuntu systems, should be used on a fresh dedicated box with minimal resources and high bandwidth/throughput**_

## On VPS Dedicated Cache Server

### Prerequisites

#### Install curl

```
sudo apt install curl
```

#### Clone Repository

```
git clone https://github.com/legendsSystems/legendsCaching.git
cd legendsCaching
```

### Installing

#### Execute build script

```
sudo chmod +x build.sh && sudo ./build.sh
```

Follow the prompts and enter the required info. Script can be run multiple times until successful if needed, just do a 'git stash && git stash drop'.

Logout and back into the ssh session to refresh perms or prepend sudo below

### Usage <a href="#usage" id="usage"></a>

TO check if the container booted properly

```
docker ps -a
```

#### Check logs

```
docker logs legendscaching-cache-1
```

## On Game Server

### Edit / Add to server.cfg

```
set sv_forceIndirectListing true
set sv_listingHostOverride "server-cache-1.example.com" # both here and the IP's below can be that of a geo based global load balancer.  Azure's Traffic Manager offering does this for next to free.
set sv_listingIPOverride "LIVE_SERVER_IP"
set sv_proxyIPRanges "CACHING_SERVER_IP_1/32" "CACHING_SERVER_IP_2/32" "CACHING_SERVER_IP_3/32" # if only one caching server, only use one entry, but can support more with a load balancer
set sv_endpoints "LIVE_SERVER_IP:LIVE_SERVER_PORT"
set adhesive_cdnKey "fv67v67gyubit67tv6767v7"  # make it up, but never change afterwards unless you want to invalidate all players cache
fileserver_add ".*" "https://server-cache-1.example.com/files"
```

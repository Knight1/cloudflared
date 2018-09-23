# Cloudflared DNS over HTTPS

[![Build Status](https://travis-ci.com/Knight1/cloudflared.svg?branch=master)](https://travis-ci.com/Knight1/cloudflared)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/672bd95d12da4f28a16190bfba379711)](https://www.codacy.com/app/Knight1/cloudflared) 
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Docker Pulls](https://img.shields.io/docker/pulls/knight/cloudflared-dns.svg)](https://hub.docker.com/r/knight/cloudflared-dns)
[![Contributions welcome](https://img.shields.io/badge/contributions-welcome-orange.svg)](https://github.com/Knight1/cloudflared/pulls)


A docker container which runs the [cloudflared](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/) proxy-dns at port 54 based on alpine with some parameters to enable DNS over HTTPS proxy for [pi-hole](https://pi-hole.net/) based on tutorials from [Oliver Hough](https://oliverhough.cloud/blog/configure-pihole-with-dns-over-https/) and [Scott Helme](https://scotthelme.co.uk/securing-dns-across-all-of-my-devices-with-pihole-dns-over-https-1-1-1-1/)

## Getting Started

Replace ```amd64``` with ```arm64``` or ```arm``` for RaspberryPi etc.

### Start with Port 54 (Default Port is 53!)

Port 54 on host network so every device on the network can reach the Proxy

```docker run -d --name cloudflared --restart=always --net host knight/cloudflared-dns:amd64```

### Start with Port 53

You can set up this Host to be a normal DNS resolver you can put into every client like with 8.8.8.8 or inside your router.

Examples: [Lifewire](https://www.lifewire.com/how-to-change-dns-servers-on-most-popular-routers-2617995), [The Verge](https://www.theverge.com/2018/4/3/17191538/how-to-change-dns-routers-windows-mac-ios)

```docker run -d --name cloudflared --restart=always -p 53:54/udp knight/cloudflared-dns:amd64```

### Pi-hole®: (A black hole for Internet advertisements)

Install Docker ```curl install.docker.com | sudo bash``` and [Pi-hole](https://hub.docker.com/r/pihole/pihole/)

```docker run -d --name cloudflared -p 127.0.0.1:54:54/udp --restart=always knight/cloudflared-dns:amd64```

Enter IP 127.0.0.1#54 into Custom 1 (IPv4) within the Pi-Hole Admin Page

## Build

You can see the build and the push to [Docker Hub](https://hub.docker.com/r/knight/cloudflared-dns/) on [Travis-CI](https://travis-ci.com/Knight1/cloudflared)

## You want to contribute? [![Awesome Badges](https://img.shields.io/badge/badges-awesome-green.svg)](https://github.com/Naereen/badges)

Issues, Pull Requests and Wiki additions are welcome :)

## test

I wrote some tests in a goss.yaml file which can be executed by [dgoss](https://github.com/aelsabbahy/goss/tree/master/extras/dgoss)

```
$ dgoss run --name cloudflared --rm -ti knight1/cloudflared:latest
INFO: Starting docker container
INFO: Container ID: e5bd35d3
INFO: Sleeping for 0.2
INFO: Running Tests
Process: cloudflared: running: matches expectation: [true]
Package: ca-certificates: installed: matches expectation: [true]
Command: cloudflared --version | head -1: exit-status: matches expectation: [0]
Command: cloudflared --version | head -1: stdout: matches expectation: [cloudflared version DEV (built unknown)]


Total Duration: 0.028s
Count: 4, Failed: 0, Skipped: 0
INFO: Deleting container
```

## License 📜
Distributed under the MIT license

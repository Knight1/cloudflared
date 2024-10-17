# Cloudflared DNS over HTTPS

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Docker Pulls](https://img.shields.io/docker/pulls/knight/cloudflared-dns.svg)](https://hub.docker.com/r/knight/cloudflared-dns)
[![Contributions welcome](https://img.shields.io/badge/contributions-welcome-orange.svg)](https://github.com/Knight1/cloudflared/pulls)


A docker container which runs the [cloudflared](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/) proxy-dns at port 54 based on alpine with some parameters to enable DNS over HTTPS proxy for [pi-hole](https://pi-hole.net/) based on tutorials from [Oliver Hough](https://oliverhough.cloud/blog/configure-pihole-with-dns-over-https/) and [Scott Helme](https://scotthelme.co.uk/securing-dns-across-all-of-my-devices-with-pihole-dns-over-https-1-1-1-1/)

## Getting Started

Replace ```amd64``` most normal PC's with ```arm64``` or ```arm``` for RaspberryPi, BananaPi etc.

### Start with Port 54 (Default Port is 53!) for testing purposes

Port 54 on host network so every device on the network can reach the Proxy

```docker run -it --rm --name cloudflared --net host knight/cloudflared-dns:amd64```

```dig example.com @IP_OF_HOST -p 54```

If you stop this container it will remove itself!

### Start with Default Port 53

You can set up this Host to be a normal DNS resolver. You can put the Host IP into every client like with 1.1.1.1 or 8.8.8.8 or inside your router so you do not need to do it manually.

Examples: [Lifewire](https://www.lifewire.com/how-to-change-dns-servers-on-most-popular-routers-2617995), [The Verge](https://www.theverge.com/2018/4/3/17191538/how-to-change-dns-routers-windows-mac-ios)

```docker run -d --name cloudflared --restart=always -p 53:54/udp knight/cloudflared-dns:amd64```

### Pi-hole®: (A black hole for Internet advertisements)

Install Docker ```curl install.docker.com | sudo bash``` and [Pi-hole](https://hub.docker.com/r/pihole/pihole/)

```docker run -d --name cloudflared -p 127.0.0.1:54:54/udp --restart=always knight/cloudflared-dns:amd64```

Enter IP 127.0.0.1#54 into Custom 1 (IPv4) within the Pi-Hole Admin Page

## Build

You can see the build, it's status and the push to [Docker Hub](https://hub.docker.com/r/knight/cloudflared-dns/) on [Travis-CI](https://travis-ci.com/Knight1/cloudflared)
I run it every week so that there is no bug in it from the upstream apline image.

## You want to contribute? [![Awesome Badges](https://img.shields.io/badge/badges-awesome-green.svg)](https://github.com/Naereen/badges)

Issues, Pull Requests and Wiki additions are very welcome 😊

## Test

```
$ dig +short @10.0.0.2 -p 54 tobiassachs.de
13.225.238.129
13.225.238.53
13.225.238.9
13.225.238.61
```

## build

Build against pinned upstream release
```
$ docker build -t knight/cloudflared-dns:latest .
```

Build against a specific upstream release
```
$ docker build -t knight/cloudflared-dns:latest . --build-arg UPSTREAM_RELEASE_TAG=2021.5.10
```

### buildx

```
$ docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
$ docker buildx build -t knight/cloudflared-dns:latest --platform linux/amd64,linux/arm/v6,linux/arm/v7 --push .
```

### dgoss

I wrote some tests in a goss.yaml file which can be executed by [dgoss](https://github.com/aelsabbahy/goss/tree/master/extras/dgoss) to test the created image

```
$ dgoss run --name cloudflared --rm -ti knight/cloudflared-dns:latest
INFO: Starting docker container
INFO: Container ID: 792bc39d
INFO: Sleeping for 0.2
INFO: Container health
INFO: Running Tests
User: cloudflared: exists: matches expectation: true
Process: cloudflared: running: matches expectation: true
Command: uname -a: exit-status: matches expectation: 0
Command: cloudflared --version: exit-status: matches expectation: 0
Command: cloudflared --version: stdout: matches expectation: ["cloudflared version 2023.8.2 (built September 2023)"]
Package: ca-certificates: installed: matches expectation: true
DNS: visibilityspots.org: resolvable: matches expectation: true


Total Duration: 0.092s
Count: 7, Failed: 0, Skipped: 0
INFO: Deleting container
```

### act

using [act](https://github.com/nektos/act#overview----) for local testing of the written github actions makes my life and commit history a lot easier;

```
$ act -l
Stage  Job ID  Job name  Workflow name  Workflow file  Events
0      test    test      CI             main.yaml      push
1      buildx  buildx    CI             main.yaml      push

$ act -j test
[CI/test] 🚀  Start image=catthehacker/ubuntu:act-latest
.
.
.
[CI/test]   ✅  Success - Main Execute Goss tests
[CI/test] 🏁  Job succeeded
```

## License 📜
[MIT Licensed](https://lbesson.mit-license.org/) (file [LICENSE](LICENSE)).

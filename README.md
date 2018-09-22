# Cloudflared

[![Build Status](https://travis-ci.org/knight1/cloudflared.svg?branch=master)](https://travis-ci.org/knight1/cloudflared)

a docker container which runs the [cloudflared](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/) proxy-dns at port 54 based on alpine with some parameters to enable DNS over HTTPS proxy for [pi-hole](https://pi-hole.net/) based on tutorials from [Oliver Hough](https://oliverhough.cloud/blog/configure-pihole-with-dns-over-https/) and [Scott Helme](https://scotthelme.co.uk/securing-dns-across-all-of-my-devices-with-pihole-dns-over-https-1-1-1-1/)

## run

Port 54 on host network

```docker run --name cloudflared --rm --net host -restart=always:5 knight1/cloudflared```

## Pi-Hole 

Install Docker and [Pi-hole](https://hub.docker.com/r/diginc/pi-hole/)

```docker run -d --name cloudflared -p 127.0.0.1:54:54 --restart=always:5 knight1/cloudflared```
Enter IP 127.0.0.1#54 into Custom 1 (IPv4)

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

## License
Distributed under the MIT license

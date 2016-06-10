---
layout: post
title: 《第一本Docker书》第一、二章
categories: [docker]
description: docker学习笔记
keywords: docker, 微服务
autotoc: true
comments: true
---

###Mac OS安装Docker

- (1). 使用docker toolbox

 在https://www.docker.com/toolbox下载最新版本toolbox。

- (2). 在os x中启动docker toolbox

启动docker cli启动docker虚拟机。

- (3). 测试docker toolbox

```
$ docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 1.11.2
Storage Driver: aufs
 Root Dir: /mnt/sda1/var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 0
 Dirperm1 Supported: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge null host
Kernel Version: 4.4.12-boot2docker
Operating System: Boot2Docker 1.11.2 (TCL 7.1); HEAD : a6645c3 - Wed Jun  1 22:59:51 UTC 2016
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 1.955 GiB
Name: default
ID: BLIX:775O:YYW5:PHDS:PFJH:SLQ3:6QMS:HNMN:YY47:NF6X:DEZU:TYTC
Docker Root Dir: /mnt/sda1/var/lib/docker
Debug mode (client): false
Debug mode (server): true
 File Descriptors: 12
 Goroutines: 29
 System Time: 2016-06-06T11:31:22.995272573Z
 EventsListeners: 0
Registry: https://index.docker.io/v1/
Labels:
 provider=virtualbox

```

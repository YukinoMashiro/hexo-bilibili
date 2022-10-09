---
title: Docker
author: Yukino
avatar: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2021-1-17 19:00:00
comments: true #是否开启评论
tags: #文章标签
  - docker
keywords: Docker #这个暂时没找到用户
description: Docker #首页文章简介
photos: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/banner/1.jpg #文章详情页的banner
---
# 1. Docker 安装

# 2. Docker 使用

## 2.1 介绍

1. docker hello world

`docker run hello-world`

![image-20210111215654933](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/image-20210111215654933.png)

2. 查看镜像

`docker images`

![image-20210111215953452](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/image-20210111215953452.png)



3. run 流程

Docker先在本地寻找镜像，如果本地有，则直接执行。若本地没有，则直接去run。若没有，则去Docker Hub上去下载到本地



## 2.2 docker 命令

### 2.2.1 帮助命令

```shell
docker version #显示 docker的版本信息
docker info #显示docker的系统信息,包括镜像和容器的数量
docker 命令 --help 
```

### 2.2.2 文档

### 2.2.3 镜像命令

`docker images` 查看本地主机所有镜像

```shell
yukino@Yukinos-Mac-mini  ~  docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   12 months ago   13.3kB

# 解释
REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像 id
CREATED 创建时间
SIZE 大小

# 可选项
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show image IDs

```

`docker search` 搜索

```shell
 yukino@Yukinos-Mac-mini  ~  docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10369     [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3842      [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   757                  [OK]
percona                           Percona Server is a fork of the MySQL relati…   519       [OK]

# 可选项
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output
      
# 通过 start 进行过滤
--filter=STARS=3000 # 不小于3000 # docker search mysql --filter=STARS=3000
```

`docker pull` 下载

```shell
 yukino@Yukinos-Mac-mini  ~  docker pull mysql
Using default tag: latest #如果不写tag，默认就是 latest
latest: Pulling from library/mysql 
6ec7b7d162b2: Pull complete  # 分层下载，docker image的核心 联合文件系统
fedd960d3481: Pull complete
7ab947313861: Pull complete
64f92f19e638: Pull complete
3e80b17bff96: Pull complete
014e976799f9: Pull complete
59ae84fee1b3: Pull complete
ffe10de703ea: Pull complete
657af6d90c83: Pull complete
98bfb480322c: Pull complete
6aa3859c4789: Pull complete
1ed875d851ef: Pull complete
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest #真实地址

# 等价
docker pull mysql
docker pull docker.io/library/mysql:latest

#指定版本
docker pull mysql:5.7

yukino@Yukinos-Mac-mini  ~  docker pull mysql:5.7
5.7: Pulling from library/mysql
6ec7b7d162b2: Already exists
fedd960d3481: Already exists
7ab947313861: Already exists
64f92f19e638: Already exists
3e80b17bff96: Already exists
014e976799f9: Already exists
59ae84fee1b3: Already exists
7d1da2a18e2e: Pull complete
301a28b700b9: Pull complete
529dc8dbeaf3: Pull complete
bc9d021dc13f: Pull complete
Digest: sha256:c3a567d3e3ad8b05dfce401ed08f0f6bf3f3b64cc17694979d5f2e5d78e10173
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

yukino@Yukinos-Mac-mini  ~  docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
mysql         5.7       f07dfa83b528   2 weeks ago     448MB
mysql         latest    a347a5928046   2 weeks ago     545MB
hello-world   latest    bf756fb1ae65   12 months ago   13.3kB
```

`docker rmi`删除

```shell
 yukino@Yukinos-Mac-mini  ~  docker rmi -f f07dfa83b #删除一个镜像
Untagged: mysql:5.7
Untagged: mysql@sha256:c3a567d3e3ad8b05dfce401ed08f0f6bf3f3b64cc17694979d5f2e5d78e10173
Deleted: sha256:f07dfa83b5283f486f1fa1628cb8b4a18a4d071a486708acc5c06243ca7f592a
Deleted: sha256:1137660d239dd339c83697d1d5cc93542333aaf35c1fd90418f9c0d166c23487
Deleted: sha256:20c29fc0161bc3cc0addc48ab1aeb70f605a5529590fd32d01502d58d1c6dc10
Deleted: sha256:8615ae1ee613441540ee54a2c517eb0600a6c83667a79f7ca74acc9ffec4c9a4
Deleted: sha256:252efab3ecb7891820c5a340645044850d6edc7815c6588450d74b0a743424f4


yukino@Yukinos-Mac-mini  ~  docker rmi -f $(docker images -aq) #删除所有镜像
Untagged: mysql:latest
Untagged: mysql@sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873
Deleted: sha256:a347a59280467629ae4d70ce555e3f96faca8ff2ff8c57cacc8184bebb681f66
Deleted: sha256:8c4db4ce0f63c9eec74a2e555bb7e2fa5e2de08389cbc747793aa30bc3ac04e1
Deleted: sha256:6179be2adc547662cd0ef2cb032b85809ac68923b7ab9c71e5a21b88bbe7f542
Deleted: sha256:17d702350e6aa7f240ca23090d24b059f5c324e292a77b5069225e76e0b51dd6
Deleted: sha256:5c5f95733957fd96300b5265d6b2df728415ed62e7b4b30eadd7d836d48187bf
Deleted: sha256:fbf118fadaf5230c8df9a3b51c608393a8f3adb99c089ea2db253438efab1a0e
Deleted: sha256:43b82d704a10e6d022fa3f31a5f827a00e339ee21dd2849a9b120ab82be9af71
Deleted: sha256:a4994702421d2b9a74c4f3810aa7ac09990e849905f23a1d8f358c826d58501f
Deleted: sha256:36c598c7a6f90abf6d67cde4a58b0747bfbcc7441d3b782bdeea7cda8c9ad7b6
Deleted: sha256:86f598b4f8200bdd4ae752f916154e4e29d5b4c211bb124eb150b9957a3e0141
Deleted: sha256:9e979d97f92bf78a225c77c6b4ba74eb2e03efb95b19b69206cd17cee15a4b26
Deleted: sha256:aff48ce4678f78d83d7e9bfb9e88cd951c3da52da08779e99b6082edd1cc66f3
Deleted: sha256:87c8a1d8f54f3aa4e05569e8919397b65056aa71cdf48b7f061432c98475eee9
Untagged: hello-world:latest
Untagged: hello-world@sha256:1a523af650137b8accdaed439c17d684df61ee4d74feac151b5b337bd29e7eec
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```

### 2.2.4 容器命令

**下载一个centos镜像来学习**

`docker pull centos`

**新建容器并启动**

`docker run [可选参数] image`

```shell
#参数说明
--name=“name” 容器名字
-d            后台运行
-it           使用交互方式运行，进入容器查看内容
-p            指定容器端口 -p 8080:8080
     -p  主机端口:容器端口
     -p  ip:主机端口:容器端口
     -p  容器端口
-P            随机指定端口

# 启动并进入容器
yukino@Yukinos-Mac-mini  ~  docker run -it centos /bin/sh
sh-4.4#
# 退出容器
exit

```

**列出所有的运行的容器**

```shell
yukino@Yukinos-Mac-mini  ~  docker ps #正在运行的容器
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
 yukino@Yukinos-Mac-mini  ~  docker ps -a # 列出当前正在运行游戏 + 历史运行过的容器
CONTAINER ID   IMAGE          COMMAND      CREATED              STATUS                          PORTS     NAMES
58058be7aaee   centos         "/bin/sh"    About a minute ago   Exited (0) 11 seconds ago                 friendly_khorana
d163d1d78b57   centos         "/bin/sh"    2 minutes ago        Exited (0) About a minute ago             charming_curie
4a7f75643f52   centos         "/bin/zsh"   3 minutes ago        Created                                   objective_ellis
f8639e957394   centos         "/bin/sh"    3 minutes ago        Exited (0) 3 minutes ago                  reverent_wing
76a01cac119f   bf756fb1ae65   "/hello"     2 hours ago          Exited (0) 2 hours ago                    frosty_edison

# 可选项
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display container IDs
  -s, --size            Display total file sizes
```



**容器退出**

```shell
exit # 容器停止退出
cmd(中间那个键) + p + q #容器不停止退出
```

**删除容器**

```shell
docker rm 容器id #删除指定容器，不能删除正在运行的容器， -f 强制删除
docker rm -f $(docker ps -aq) #删除所有容器 
docker ps -a -q|xargs docker rm -f  #删除所有容器
```

**启动和停止容器的操作**

```shell
docker start 容器id   # 启动容器
docker restart 容器id # 重启容器
docker stop 容器id    # 停止容器
docker kill 容器id    # 强制停止当前容器
```

### 2.2.5 常用其他命令

**后台启动容器**

```shell
yukino@Yukinos-Mac-mini  ~  docker run -d centos # 后台启动容器
0ed9981b2b99a49d7a6badda06d6b5214adf7f4abd12e69b59a3740944c86da2
 yukino@Yukinos-Mac-mini  ~  docker ps # 发现没有起来
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

# docker 容器使用后台运行，必须要一个前台进程，docker发现没有应用，就会自动停止
# nginx 容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
# 自己编写一段shell脚本
yukino@Yukinos-Mac-mini  ~  docker run -d centos /bin/sh -c "while true;do echo hello;sleep 1;done"
7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859
# 查看容器
 yukino@Yukinos-Mac-mini  ~  docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
7e367211d5e0   centos    "/bin/sh -c 'while t…"   6 seconds ago   Up 5 seconds             mystifying_ardinghelli
# 查看log
 yukino@Yukinos-Mac-mini  ~  docker logs -tf --tail 10 7e367211d5e0
2021-01-11T16:03:59.195128830Z hello
2021-01-11T16:04:00.197724751Z hello
2021-01-11T16:04:01.200070913Z hello
2021-01-11T16:04:02.204645447Z hello
2021-01-11T16:04:03.209292239Z hello

# 可选项
Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
```

**查看容器中的进程信息**

```shell
 yukino@Yukinos-Mac-mini  ~  docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
7e367211d5e0   centos    "/bin/sh -c 'while t…"   5 minutes ago   Up 5 minutes             mystifying_ardinghelli

# 查看进程信息
 yukino@Yukinos-Mac-mini  ~  docker top 7e367211d5e0
PID                 USER                TIME                COMMAND
3248                root                0:00                /bin/sh -c while true;do echo hello;sleep 1;done
3641                root                0:00                {sleep} /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```



**查看镜像元数据**

```shell
docker inspect 7e367211d5e0
```

```json
[
    {
        "Id": "7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859",
        "Created": "2021-01-11T16:03:29.779361521Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo hello;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 3248,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-01-11T16:03:30.085701324Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859/hostname",
        "HostsPath": "/var/lib/docker/containers/7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859/hosts",
        "LogPath": "/var/lib/docker/containers/7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859/7e367211d5e06cf782c23ed36e8bd915e79c7d37290f340b08f49c121ac13859-json.log",
        "Name": "/mystifying_ardinghelli",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/acddf2185fe63f216521b671abaeca46264f46939e439026c4b0a6151da02c5d-init/diff:/var/lib/docker/overlay2/329520e3e0d88d8454fba204a80c88b7f6ef891bb8dbd6691761ac4d95c5177d/diff",
                "MergedDir": "/var/lib/docker/overlay2/acddf2185fe63f216521b671abaeca46264f46939e439026c4b0a6151da02c5d/merged",
                "UpperDir": "/var/lib/docker/overlay2/acddf2185fe63f216521b671abaeca46264f46939e439026c4b0a6151da02c5d/diff",
                "WorkDir": "/var/lib/docker/overlay2/acddf2185fe63f216521b671abaeca46264f46939e439026c4b0a6151da02c5d/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "7e367211d5e0",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo hello;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "273cc9e3080fa62dc986a5c0b8da4ef8c86edaa2f7af92a241e388638c7647c6",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/273cc9e3080f",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "d147d056858d03efcdb9ce0a1a14552873ebe1c87c55514523def2dc879b206b",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "7c3627e0ba0582a218c280f8fbb0bd6cca3d3325582b5cedd1b8b6410de986cd",
                    "EndpointID": "d147d056858d03efcdb9ce0a1a14552873ebe1c87c55514523def2dc879b206b",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```



**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

yukino@Yukinos-Mac-mini  ~  docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
7e367211d5e0   centos    "/bin/sh -c 'while t…"   13 minutes ago   Up 13 minutes             mystifying_ardinghelli

# 方式1:以交互模式进入容器
yukino@Yukinos-Mac-mini  ~  docker exec -it 7e367211d5e0 /bin/bash
[root@7e367211d5e0 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 16:03 ?        00:00:00 /bin/sh -c while true;do echo hello;sleep 1;done
root       864     0  0 16:17 pts/0    00:00:00 /bin/bash
root      1023     1  0 16:20 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root      1024   864  0 16:20 pts/0    00:00:00 ps -ef

# 方式2: docker attach
yukino@Yukinos-Mac-mini  ~  docker attach e367211d5e
正在执行当前的代码

# docker exec   # 进入容器开启一个新的终端，可以在里面操作(常用)
# docker attach # 进入容器正在执行的终端，不会启动新的进程
```



**从容器内拷贝文件到主机上**

```shell
docker cp f03fddef487a:/home/tmp.md .

# 拷贝是一个手动过程，未来我们通过-V 卷的技术，可以实现自动同步 /home /home
```

### 2.2.6 docker 命令小结

 ![2018071915491757](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/2018071915491757.png)

### 2.2.7 docker tomcat启动

```shell
# 官方使用
docker run -it --rm tomcat:9.0# 一般用来测试，用完就删除容易(镜像还在)，一般不这样用

# 下载启动
docker pull tomcat:9.0
#启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat:9.0 #后台启动，宿主机端口映射3355映射到容器端口8080

# 发现浏览器访问 http://localhost:3355/ 出错。
#进入tomcat
docker exec -it tomcat01 /bin/sh
进入 tomcat下的webapps，发现为空
# 拷贝内容，刷新浏览器访问 http://localhost:3355/，成功
cp -r webapps.dist/* webapps/


```



### 2.2.8 可视化

**portainer**

```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

### 2.2.9 commit 镜像

```shell
# 启动容器
docker run -it -p 8080:8080 tomcat:9.0
# 进入容器
docker exec -it d239c0637046 /bin/sh
# 添加webapps应用
cp -r webapps.dist/* webapps/
# commit 成一个新镜像
yukino@Yukinos-Mac-mini  ~  docker commit -a="yukino" -m="add webapps app" d239c0637046 tomcat02:1.0
sha256:98532da3cc1bbd014972e720081c187023f2172f9caaced66e94cd2f5c48b85c
 yukino@Yukinos-Mac-mini  ~  docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
tomcat02              1.0       98532da3cc1b   5 seconds ago   653MB

# 使用一个新镜像
docker run -it -p 8081:8080 tomcat02:1.0

```

# 3. 容器数据卷

## 3.1 使用卷技术

```shell
docker run -it -v 主机目录:容器目录
docker run -it -v /Users/yukino/Desktop/temp/test:/home centos /bin/sh
```

```shell
# 查看挂载信息
docker inspect 5f48c7d36c11
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/Users/yukino/Desktop/temp/test",
                "Destination": "/home",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
```

两个文件夹数据共享,将容器删除掉，数据依然能够保存

## 3.2 mysql

```shell
# 官方测试代码
docker run --name some-mysql -v /my/own/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag


-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名
# docker run -d -p 3310:3306 -v /Users/yukino/Desktop/temp/mysql/conf:/etc/mysql/conf.d -v /Users/yukino/Desktop/temp/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:8.0

```



## 3.3 具名和匿名挂载

```shell
docker volume
Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes
  

# 查看所有volume
docker volume ls 
# 匿名挂载
yukino@Yukinos-Mac-mini  ~/Desktop/temp/mysql/data  docker run -d -P --name nginx01 -v /etc/nginx nginx
yukino@Yukinos-Mac-mini  ~/Desktop/temp/mysql/data  docker volume ls
DRIVER    VOLUME NAME
local     3510004e6812e29dbbbda68b229b61e8cda715d573eef744bd470843014bea88
local     8388160e20f4753e11b20d159c0eca0eb8f269fdbe1e37274874bf0dc85871e9

# 匿名挂载挂载即 -v 只写了容器内的路径，没有写容器外的路径

# 具名挂载
-v 卷名:容器路径
yukino@Yukinos-Mac-mini  ~/Desktop/temp/mysql/data  docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
9656638bbc24aabdfc5961eaa7824c508b6fb7e152c78ab16b7af50dcc3f7a92
 yukino@Yukinos-Mac-mini  ~/Desktop/temp/mysql/data  docker volume ls
DRIVER    VOLUME NAME
local     3510004e6812e29dbbbda68b229b61e8cda715d573eef744bd470843014bea88
local     8388160e20f4753e11b20d159c0eca0eb8f269fdbe1e37274874bf0dc85871e9
local     juming-nginx

# 通过卷名，查看卷信息
 yukino@Yukinos-Mac-mini  ~/Desktop/temp/mysql/data  docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2021-01-12T16:36:27Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

如何确定是具名还是匿名

```shell
-v 容器内路径 # 匿名
-v 卷名:容器内路径 # 具名
-v /宿主机路径:容器内路径 # 指定挂载路径 
```

拓展

```shell
docker run -d -P --name nginx02 -v  juming-nginx:/etc/nginx:ro nginx # ro 只读
docker run -d -P --name nginx02 -v  juming-nginx:/etc/nginx:ro nginx # rw 可读可写
# 一旦这个设置了容器权限，容器对我们挂载出来的内容就有限定了
# ro 只要看到说明这个路径只能通过宿主机来操作，容器内部是无法操作的
```

## 3.4  Docker file 挂载

```shell
# dockerfile 内容 
# 基于centos镜像
FROM centos
# 挂载两个匿名卷
VOLUME ["volume01","volume02"]
# 输出echo
CMD echo "---end---"
CMD /bin/bash
#这里的每个命令都是镜像的一层

# 生成镜像
docker build -f dockerfile1 -t yukinocentos:1.0 . 
```

```shell
#执行镜像
✘ yukino@Yukinos-Mac-mini  ~/Desktop/temp/dockerfile  docker run -it yukinocentos:1.0 /bin/sh
sh-4.4# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02 # 挂载的镜像

# 退出查看原数据
yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                               NAMES
1c6ec60a439f   yukinocentos:1.0      "/bin/sh"                2 minutes ago   Up 2 minutes
#查看原数据
docker inspect 1c6ec60a439f

        "Mounts": [
            {
                "Type": "volume",
                "Name": "e3002d4d1b7a1ea25504a7fdbdae8d1f103924c3b87930bb004a2e6c49259a67",
                "Source": "/var/lib/docker/volumes/e3002d4d1b7a1ea25504a7fdbdae8d1f103924c3b87930bb004a2e6c49259a67/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "3ecffa507747fce5d61ab4779ceb9ce1a458167829d63101ab89d332f93cd49c",
                "Source": "/var/lib/docker/volumes/3ecffa507747fce5d61ab4779ceb9ce1a458167829d63101ab89d332f93cd49c/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
        
不知道为啥，source 目录在mac上打不开

```

## 3.5 数据卷容器

多个mysql容器之间数据同步

```shell
docker run -it --name docker01 yukinocentos:1.0
docker run -it --name docker02 --volumes-from docker01 yukinocentos:1.0 #相当于docker02继承了docker01
# 两个容器的数据卷volume01与volume02分别同步 
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02

# 创建docker03
docker run -it --name docker03 --volumes-from docker01 yukinocentos:1.0

docker01 docker02 docker03 数据卷共享
```

当删除docker01后，docker02 docker03 数据依然存在

 # 4. DockerFile

## 4.1 命令

- 每个指令（关键字） 都是大写字母

```shell
FROM       # 基础镜像，一切从这里开始构建 centos
MAINTAINER # 镜像谁写的，姓名+邮箱
RUN        # 镜像构建的时候需要运行的命令
ADD        # 步骤：tomcat镜像，这个tomcat压缩包，添加内容
WORKDIR    # 设置镜像的工作目录
VOLUME     # 挂载的目录
EXPOSE     # 指定对外的端口
RUN        # 容器启动后要做的事
CMD        # 指定容器启动的时候要运行的命令，只有最后一个会被生效
ENTRYPOINT # 指定这个容器的时候要运行的命令，可以追加命令
ONBUILD    # 当构建一个被继承 DockerFile 这个时候就会运行 ONBUILD 的指令。触发指令
COPY       # 类似ADD，将文件拷贝到镜像中
ENV        # 构建时设置环境变量
```

## 4.2 构建自己的centos

Docker Hub 中大部分镜像都是从这个基础镜像过来的`FROM scratch`，然后再构建

```shell
FROM centos
MAINTAINER yukino<233@qq.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80
CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash
```

```shell
# 构建镜像
yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker build -f yukinocentos -t mycentos:0.1 .
[+] Building 168.9s (8/8) FINISHED
 => [internal] load build definition from yukinocentos                                                            0.0s
 => => transferring dockerfile: 241B                                                                              0.0s
 => [internal] load .dockerignore                                                                                 0.0s
 => => transferring context: 2B                                                                                   0.0s
 => [internal] load metadata for docker.io/library/centos:latest                                                  0.0s
 => CACHED [1/4] FROM docker.io/library/centos                                                                    0.0s
 => [2/4] WORKDIR /usr/local                                                                                      0.0s
 => [3/4] RUN yum -y install vim                                                                                163.2s
 => [4/4] RUN yum -y install net-tools                                                                            5.1s
 => exporting to image                                                                                            0.5s
 => => exporting layers                                                                                           0.5s
 => => writing image sha256:018a20376d20419103f1e2941e3d932fd376380551617692bf6bf336b5932ca4                      0.0s
 => => naming to docker.io/library/mycentos:0.1
```



```shell
 # 启动image
 yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker run -it mycentos:0.1
[root@f2e629f64672 local]# pwd
/usr/local
# vim ifconfig 可以使用了
```

```shell
# 查看构建过程
docker history 

 yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker history mycentos:0.1
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
018a20376d20   4 minutes ago   CMD ["/bin/sh" "-c" "/bin/bash"]                0B        buildkit.dockerfile.v0
<missing>      4 minutes ago   CMD ["/bin/sh" "-c" "echo \"---end---\""]       0B        buildkit.dockerfile.v0
<missing>      4 minutes ago   CMD ["/bin/sh" "-c" "echo $MYPATH"]             0B        buildkit.dockerfile.v0
<missing>      4 minutes ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c yum -y install net-tools # bu…   14.4MB    buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c yum -y install vim # buildkit    58.1MB    buildkit.dockerfile.v0
<missing>      7 minutes ago   WORKDIR /usr/local                              0B        buildkit.dockerfile.v0
<missing>      7 minutes ago   ENV MYPATH=/usr/local                           0B        buildkit.dockerfile.v0
<missing>      7 minutes ago   MAINTAINER yukino<233@qq.com>                   0B        buildkit.dockerfile.v0
<missing>      5 weeks ago     /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      5 weeks ago     /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>      5 weeks ago     /bin/sh -c #(nop) ADD file:bd7a2aed6ede423b7…   209MB
```

## 4.3 CMD 与 ENTRYPOINT

```shell
ENTRYPOINT # 指定这个容器的时候要运行的命令，可以追加命令
ONBUILD    # 当构建一个被继承 DockerFile 这个时候就会运行 ONBUILD 的指令。触发指令
```

测试 CMD

```shell
# DockerFile cmd-test
yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  cat cmd-test
FROM centos
CMD ["ls","-a"]
# 构建镜像
docker build -f cmd-test -t cmdtest .

# 执行
 ✘ yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker run -it cmdtest
.   .dockerenv	dev  home  lib64       media  opt   root  sbin	sys  usr
..  bin		etc  lib   lost+found  mnt    proc  run   srv	tmp  var

# 追加命令,报错, -l 替换了 CMD ["ls","-a"]， -l不是命令，所以报错
yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker run -it cmdtest -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:370: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.

# 追加命令 ls -al 可行
 ✘ yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker run -it cmdtest ls -al
total 56
drwxr-xr-x   1 root root 4096 Jan 13 15:28 .
drwxr-xr-x   1 root root 4096 Jan 13 15:28 ..
-rwxr-xr-x   1 root root    0 Jan 13 15:28 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3 15:22 bin -> usr/bin
drwxr-xr-x   5 root root  360 Jan 13 15:28 dev
drwxr-xr-x   1 root root 4096 Jan 13 15:28 etc
drwxr-xr-x   2 root root 4096 Nov  3 15:22 home
lrwxrwxrwx   1 root root    7 Nov  3 15:22 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3 15:22 lib64 -> usr/lib64
drwx------   2 root root 4096 Dec  4 17:37 lost+found
drwxr-xr-x   2 root root 4096 Nov  3 15:22 media
drwxr-xr-x   2 root root 4096 Nov  3 15:22 mnt
drwxr-xr-x   2 root root 4096 Nov  3 15:22 opt
dr-xr-xr-x 148 root root    0 Jan 13 15:28 proc
dr-xr-x---   2 root root 4096 Dec  4 17:37 root
drwxr-xr-x  11 root root 4096 Dec  4 17:37 run
lrwxrwxrwx   1 root root    8 Nov  3 15:22 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov  3 15:22 srv
dr-xr-xr-x  13 root root    0 Jan 13 15:28 sys
drwxrwxrwt   7 root root 4096 Dec  4 17:37 tmp
drwxr-xr-x  12 root root 4096 Dec  4 17:37 usr
drwxr-xr-x  20 root root 4096 Dec  4 17:37 var
```

测试 ENTRYPOINT

```shell
# Docker file
yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  cat dockerfile-entrypoint
FROM centos
ENTRYPOINT ["ls","-a"]

# 构建
docker build -f dockerfile-entrypoint -t entrypointtest .

# 追加命令 -l 追加到了 ENTRYPOINT ["ls","-a"]，不会报错
yukino@Yukinos-Mac-mini  ~/Desktop/temp/test  docker run -it entrypointtest -l
total 56
drwxr-xr-x   1 root root 4096 Jan 13 15:32 .
drwxr-xr-x   1 root root 4096 Jan 13 15:32 ..
-rwxr-xr-x   1 root root    0 Jan 13 15:32 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3 15:22 bin -> usr/bin
drwxr-xr-x   5 root root  360 Jan 13 15:32 dev
drwxr-xr-x   1 root root 4096 Jan 13 15:32 etc
drwxr-xr-x   2 root root 4096 Nov  3 15:22 home
lrwxrwxrwx   1 root root    7 Nov  3 15:22 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3 15:22 lib64 -> usr/lib64
drwx------   2 root root 4096 Dec  4 17:37 lost+found
drwxr-xr-x   2 root root 4096 Nov  3 15:22 media
drwxr-xr-x   2 root root 4096 Nov  3 15:22 mnt
drwxr-xr-x   2 root root 4096 Nov  3 15:22 opt
dr-xr-xr-x 150 root root    0 Jan 13 15:32 proc
dr-xr-x---   2 root root 4096 Dec  4 17:37 root
drwxr-xr-x  11 root root 4096 Dec  4 17:37 run
lrwxrwxrwx   1 root root    8 Nov  3 15:22 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov  3 15:22 srv
dr-xr-xr-x  13 root root    0 Jan 13 15:28 sys
drwxrwxrwt   7 root root 4096 Dec  4 17:37 tmp
drwxr-xr-x  12 root root 4096 Dec  4 17:37 usr
drwxr-xr-x  20 root root 4096 Dec  4 17:37 var
```

## 4.4 构建tomcat

![image-20210113235207052](/Users/yukino/Library/Application Support/typora-user-images/image-20210113235207052.png)

# 5.发布镜像

## 5.1 阿里云

- 进入阿里云容器镜像服务，创建镜像仓库

![image-20210114225137608](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/image-20210114225137608.png)

![image-20210114225202674](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/image-20210114225202674.png)

![image-20210114225233923](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/image-20210114225233923.png)

- push 到 阿里云的步骤参考 阿里云里面的操作步骤即可

# 6.docker 网络

```shell
# 运行容器
docker run -d -P --name tomcat01 tomcat
# 执行 ip addr
docker exec -it c864d7b91d46 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
3: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN group default qlen 1000
    link/tunnel6 :: brd ::
50: eth0@if51: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```


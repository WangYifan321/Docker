# 常用命令-核心

```shell
docker run 容器id （可以在后面加cmd命令）
docker run -it centos /bin/bash
docker rm -f $(docker ps -aq)
docker images
docker pull
docker ps (-a-所有包括历史，-q-只显示id)
docker start 容器id
docker restart 容器id
docker stop 容器id      # 停止当前正在运行的容器
docker kill 容器id      # 强制停止当前容器
docker logs -f -t --tail n条 容器id
docker inspect 容器id
```

# Docker学习



https://docs.docker.com/engine/install/centos/

## 安装docker



```shell
# 卸载老版本的docker
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 需要的安装包
sudo yum install -y yum-utils
# 设置仓库镜像
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 更新yum安装包索引
 yum makecache fast
# 安装docker
 sudo yum install docker-ce docker-ce-cli containerd.io
# 启动docker
systemctl start docker
# 检查版本
 docker version
# 查看镜像
 docker images
```

## 卸载docker

```shell
# 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 删除资源,即docker的默认工作路径
re -rf /var/lib/docker
```

## 配置阿里云加速

##  配置镜像加速器

针对Docker客户端版本大于 1.10.0 的用户

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://86ueeqey.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# 常用命令

## 帮助命令

```shell
docker version     # 显示docker的版本信息
docker 命令 --help  # 帮助命令
docker info        # 显示docker的系统信息，包括镜像和容器的数量
```



## 镜像命令

**docker images 查看所有本地的主机上的镜像**

```shell
[root@wyf ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   4 months ago   13.3kB

# 解释
REPOSITORY   镜像的仓库源
TAG          镜像的标签
IMAGE ID     镜像的id
CREATED      镜像的创建时间
SIZE         镜像的大小

# 可选项
  -a， --all     列出所有的镜像
  -q， --quiet   只显示镜像的id
```

**docker search 搜索镜像**

```shell
[root@wyf ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   11094     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4203      [OK]       
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   823                  [OK]

```

**docker pull  下载镜像**

下载的东西一定是docker hub仓库中存在的

```shell
docker pull mysql
docker pull mysql:5.7
```

**docker rmi 删除镜像**

```shell
docker rmi -f $(docker images -aq)   删除所有的容器
docker rmi -f 容器id 容器id 容器id
```



## 容器命令

**有镜像才可以创建容器**

**新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
--name="name"    容器名字 tomcat01 tomcat02，用来区分容器
-d               后台方式运行
-it              使用交互的方式运行，进入容器查看内容
-p               指定容器的端口 -p  8080:8080
     -p ip:主机端口:容器端口
     -p 主机端口：容器端口（最常用）
     -p 容器端口
     容器端口
-P               随机指定一个端口

# 进入容器
[root@wyf wangyifan]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
centos       latest    300e315adb2f   7 months ago   209MB
[root@wyf wangyifan]# docker run -it centos /bin/bash
[root@1849c645669d /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

# 退出容器，容器停止并退出
[root@1849c645669d /]# exit
exit

# 退出容器，容器不停止并突出
ctrl+p+q
```

**列出所有的容器**

```shell
docker ps
   列出当前正在运行的容器
-a 列出当前正在运行的容器+历史运行过的容器
-n=？ 显示最近？条创建的容器 
-q 只显示容器的编号
```

**删除容器**

```shell
docker rm 容器id    # 不能删除正在运行的容器，强制删除加上 -f
docker rm -f $(docker ps -aq)
docker ps -a -q|xargs docker rm  # 通过管道符删除
```

**启动和停止容器的操作**

```shell
docker start 容器id
docker restart 容器id
docker stop 容器id      # 停止当前正在运行的容器
docker kill 容器id      # 强制停止当前容器
```

## 常用的其他命令

**后台启动容器**

```shell
docker run -d 镜像名
 # 启动之后发现镜像停止了，因为容器发现自己没有提供服务，自杀了
```

**查看日志**

```shell
docker logs -f -t --tail n条 容器id


[root@wyf wangyifan]# docker run -d centos /bin/bash
f9da3b25e9131aeed0cdbfed511bccedcfcf79093d8da9959f6d7cb6f3bea37e
[root@wyf wangyifan]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
centos       latest    300e315adb2f   7 months ago   209MB
[root@wyf wangyifan]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@wyf wangyifan]# clear
# 自己编写脚本让容器运行
[root@wyf wangyifan]# docker run -d centos /bin/sh -c "while true;do echo wangyifan;sleep 1;done"
6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209
[root@wyf wangyifan]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
6fe12dec7a58   centos    "/bin/sh -c 'while t…"   6 seconds ago   Up 5 seconds             silly_archimedes
[root@wyf wangyifan]# docker logs -ft --tail 10 6fe12dec7a58
2021-07-06T08:54:20.957571735Z wangyifan
2021-07-06T08:54:21.959664356Z wangyifan
2021-07-06T08:54:22.961571030Z wangyifan
2021-07-06T08:54:23.963830125Z wangyifan
2021-07-06T08:54:24.965858830Z wangyifan
2021-07-06T08:54:25.967790481Z wangyifan
2021-07-06T08:54:26.969872712Z wangyifan
2021-07-06T08:54:27.972034960Z wangyifan
2021-07-06T08:54:28.974232020Z wangyifan
2021-07-06T08:54:29.976206157Z wangyifan
2021-07-06T08:54:30.978359644Z wangyifan
2021-07-06T08:54:31.980310558Z wangyifan
2021-07-06T08:54:32.982212248Z wangyifan
^C
```



**查看容器中进程信息**

```shell
[root@wyf wangyifan]# docker top 6fe12dec7a58
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                3000                2979                0                   16:53               ?                   00:00:00            /bin/sh -c while true;do echo wangyifan;sleep 1;done
root                3348                3000                0                   16:56               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1

```

**查看容器信息**

```shell
docker inspect 容器id

[root@wyf wangyifan]# docker inspect 6fe12dec7a58
[
    {
        "Id": "6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209",
        "Created": "2021-07-06T08:53:11.485495739Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo wangyifan;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 3000,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-07-06T08:53:11.780058574Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209/hostname",
        "HostsPath": "/var/lib/docker/containers/6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209/hosts",
        "LogPath": "/var/lib/docker/containers/6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209/6fe12dec7a58cac21b53257ce0e42c15f6cf91f463e304d6e26f84a23ed47209-json.log",
        "Name": "/silly_archimedes",
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
            "CgroupnsMode": "host",
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
                "LowerDir": "/var/lib/docker/overlay2/2b2255fd46d09891fd0e28a6d205b4f56b17830a6f7aad369e76b858fcc95071-init/diff:/var/lib/docker/overlay2/612c2af4cc8a4cf12365fa550332a8f3a93e49c33f21a46459cd485002d653d1/diff",
                "MergedDir": "/var/lib/docker/overlay2/2b2255fd46d09891fd0e28a6d205b4f56b17830a6f7aad369e76b858fcc95071/merged",
                "UpperDir": "/var/lib/docker/overlay2/2b2255fd46d09891fd0e28a6d205b4f56b17830a6f7aad369e76b858fcc95071/diff",
                "WorkDir": "/var/lib/docker/overlay2/2b2255fd46d09891fd0e28a6d205b4f56b17830a6f7aad369e76b858fcc95071/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "6fe12dec7a58",
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
                "while true;do echo wangyifan;sleep 1;done"
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
            "SandboxID": "fa035c6acdc42a6e012236ff01c371c2950bf90362b5fed403a77b4356081309",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/fa035c6acdc4",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "aa8bde402918e176053285c7382563b53e14df5a29d54b8a0a3d9848de7be2d6",
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
                    "NetworkID": "0d8f5f39d543fbd03d8a455f13bb20bb79cf4bf887eb39af6fba8ec3afc29673",
                    "EndpointID": "aa8bde402918e176053285c7382563b53e14df5a29d54b8a0a3d9848de7be2d6",
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
# 容器通常是后台运行的，如何进入容器，修改配置？

docker exec -it 容器id /bin/bash

[root@wyf wangyifan]# clear
[root@wyf wangyifan]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
6fe12dec7a58   centos    "/bin/sh -c 'while t…"   8 minutes ago   Up 8 minutes             silly_archimedes
[root@wyf wangyifan]# docker exec -it 6fe12dec7a58 /bin/bash
[root@6fe12dec7a58 /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@6fe12dec7a58 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 08:53 ?        00:00:00 /bin/sh -c while true;do echo wangyifan;sleep 1
root       519     0  0 09:01 pts/0    00:00:00 /bin/bash
root       541     1  0 09:01 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sle
root       542   519  0 09:01 pts/0    00:00:00 ps -ef

docker attach 容器id
```

docker exec-------------> 进入容器后开启一个新的终端，可以在里面操作（常用）

docker attach--------------> 进入当前正在运行的容器，不会启动新的进程

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 主机路径
```

# 安装练习

## Docker安装Nginx

```shell
[root@wyf ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
b4d181a07f80: Pull complete 
66b1c490df3f: Pull complete 
d0f91ae9b44c: Pull complete 
baf987068537: Pull complete 
6bbc76cbebeb: Pull complete 
32b766478bc2: Pull complete 
Digest: sha256:3ca76089b14cf7db77cc5d4f3e9c9eb73768b9c85a0eabde1046435a6aa41c06
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
[root@wyf ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    4cdc5dd7eaad   8 hours ago    133MB
centos       latest    300e315adb2f   7 months ago   209MB
[root@wyf ~]# docker run -d --name nginx01 -p 8081:80 nginx
b49c1cde970a06c7a1e014738a777ef89b0adfcb0af14300cef732978554250d

```

## Docker安装tomcat

没有镜像的化可以直接运行，会自动的从docker hub上拉取

```shell
# 官方使用，用完即删除
docker run -it --rm tomcat:9.0

# 下载再启动
docker pull tomcat

# 启动运行
docker run -d -p 8080:8080 --name tomcat01 tomcat

# 进入容器
docker exec -it tomcat01 /bin/bash
# Linux命令少了，没有了webapps，阿里云镜像默认使用了最小的镜像，# 所有不必要的都被删除掉了，保证最小的可运行的环境。

```

# 可视化

portainer

```shell
# 安装
docker run -d -p 1227:9000 --restart=always -v /var/run/docker.sock:/var/run/dock.sock --privileged=true portainer/portainer

# 测试
http://47.111.190.15:1227/
```

# Commit镜像

```shell
docker commit -a="wangyifan" -m="add webapps app" 容器id tomcat02:1.0(新生成的容器名:版本号)

-a  # 作者
-m  # 类似于git的-m，附加的信息
```

# 容器数据卷

**将容器内的某一个文件和容器外的进行双向绑定**

```shell
docker run -it -v 主机目录:容器内目录

docker run -it -v /home/ceshi:/home centos /bin/bash  # 根据镜像开启一个新的容器

docker start 容器id  # 启动一个已有的容器
docker attach 容器id # 不开启新终端进入容器
```

## 具名和匿名挂载

```shell
-v 容器内路径          # 匿名挂载
-v 卷名:容器内路径      # 具名挂载
-v /宿主机路径:容器内路径 # 指定路径挂载

docker volume --help

# 匿名挂载
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的volume的情况
docker volume ls

# 匿名挂载
docker run -d -P --name nginx02 -v jvming-nginx02:/etc/nginx nginx
```

可以改权限：让容器内的文件只能在容器外读写，在容器内的路径后加:ro 或者：rw

# DockerFile

## 基础语法

```shell
FROM       # 基础镜像，一切从这里开始构建
MAINTAINER # 镜像是谁写的，姓名+邮箱
RUN        # 镜像构建的时候需要运行的指令
ADD        # 步骤，tomcat镜像，这个tomcat压缩包，添加内容
WORKDIR    # 镜像的工作目录
VOLUME     # 挂载的目录
EXPOSE     # 指定对外的端口
CMD        # 指定这个容器启动的时候要运行的命令，只有最后一个会生效
ENTRYPOINT # 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD    # 当构建一个被继承 Dockerfile 这个时候就会运行 ONBUILD 命令，触发指令
COPY       # 类似ADD ，将我们文件拷贝到镜像中
ENV        # 构建的时候设置环境变量，比如设置最大可用内存，MySQL的密码等
```

## 实战应用

创建一个自己的centos

```shell
# 1、创建dockerfile文件
[root@wyf dockerfile]# vim mydockerfile-centos 

FROM centos
MAINTAINER wangyifan<877801903@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD  echo $MYPATH
CMD echo "-----end---"
CMD /bin/bash


# 2、通过这个文件构建镜像
  docker build -f mydockerfile-centos -t mycentos:0.1 .
  -f file选择文件
  -t target目标
  
  
# 3、测试运行
  
```

**docker history可以查看镜像的构建过程**

>CMD和ENTRYPOINT的区别

cmd 命令会被覆盖，相当于只能有一个cmd命令

```shell
CMD["ls","-a"]

docker run 容器id -l  # -l相当于追加了一条cmd命令
```

entrypoint不会被覆盖，可以追加命令

## 实战tomcat

1、准备镜像文件和压缩包

2、编写dockerfile

```shell
FROM centos
MAINTAINER wangyifan<wangyifan52199@163.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-11_linux-x64_bin.tar.gz /usr/local
ADD apache-tomcat-9.0.50.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk-11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.50
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.50
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080
CMD /usr/local/apache-tomcat-9.0.50/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.50/bin/logs/catalina.out

```

3、docker build -t name .

​     -t 后面跟上要生成镜像的名字

​      **不要把点漏掉**

4、启动

```shell
docker run -d -p 1227:8080 --name wangyifantomcat -v /home/wangyifan/diytomcat/test:/usr/local/apache-tomcat-9.0.50/webapps/test -v /home/wangyifan/diytomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.50/logs diytomcat
```

5、进入容器查看

```shell
docker exec -it fa9d2d0a37cd /bin/bash
```

6、之前进行了卷挂载，现在可以直接发布项目

## 发布镜像到dockerhub

```shell
[root@wyf ~]# docker login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[root@wyf ~]# docker login -u wangyifan52199
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded


[root@wyf ~]# docker tag e139b5cf37ec wangyifan52199/tomcat:1.0
[root@wyf ~]# docker push wangyifan52199/tomcat:1.0
```

发布到dockerhub时必须加上自己dockerhub的用户名和版本号

## 发布镜像到阿里云仓库



# Docker 网络

查看ip

```shell
ip addr
```

```shell
--link  # 相当于在容器的。etc/host文件中配置，不推荐使用
```

## 自定义网络

自己定义一个网络，不适用docker0那个网关

```shell
[root@wyf ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
f6b7fb7b2111922b98ca9abd124f0026a42c315d1e7ff621d7a0816e461f3c5b
[root@wyf ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
0d8f5f39d543   bridge    bridge    local
0f3b1aa83cae   host      host      local
f6b7fb7b2111   mynet     bridge    local
ec8d25ca4fa6   none      null      local
[root@wyf ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "f6b7fb7b2111922b98ca9abd124f0026a42c315d1e7ff621d7a0816e461f3c5b",
        "Created": "2021-07-13T16:54:30.631649366+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
[root@wyf ~]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
ead3de99b193864102297e08100902f4a10bda050bd6453632bd900664a354d0
[root@wyf ~]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
386147fb8bd24a1f42cbd660447f3d5d21253dace9a36dc39a525614df23dc29

[root@wyf ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "f6b7fb7b2111922b98ca9abd124f0026a42c315d1e7ff621d7a0816e461f3c5b",
        "Created": "2021-07-13T16:54:30.631649366+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "386147fb8bd24a1f42cbd660447f3d5d21253dace9a36dc39a525614df23dc29": {
                "Name": "tomcat-net-02",
                "EndpointID": "9368c9a834d779a655df096927e45b1f11ca055e369d5e8fd1f7ddebb42be6a0",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "ead3de99b193864102297e08100902f4a10bda050bd6453632bd900664a354d0": {
                "Name": "tomcat-net-01",
                "EndpointID": "040021b957a21340a9a2cee4c4faeed3d3cddac081adc309c4b050a1b5cafd75",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
[root@wyf ~]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.079 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.082 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.084 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=4 ttl=64 time=0.071 ms
^C
--- tomcat-net-02 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3ms
rtt min/avg/max/mdev = 0.071/0.079/0.084/0.005 ms
```

## 不同网络链接

不同的网络之间不能连接，但是一个网络下的容器可以和另一个网络进行链接

```shell
docker network connect 网络名 容器名
# 即可以实现一个容器和另一个网络打通
```



# spring boot微服务打包docker镜像

1、编写dockerfile，可以在idea中写，插件中搜索docker下载一个写dockerfile的插件

```shell
FROM java:8

COPY *.jar /app.jar

CMD ["--server.port=8080"]

EXPOSE 8080
ENTRYPOINT ["java","-jar","/app.jar"]
```

2、发布镜像

```shell
# 把相应的jar包和dockerfile传到服务器上
# docker build -t springboot-app
# docker run -d -P --name wangyifan-spring springboot-app
# 然后spring项目就会在容器中跑起来了
```






















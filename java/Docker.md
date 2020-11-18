# Docker镜像加速

---



1. 登录阿里云找到容器镜像服务
2. 按照阿里云给出的文档进行配置即可

# Docker的常用命令

## 帮助命令

```shell
docker version		# 显示docker的版本信息
docker info				# 显示docker的系统信息,包括镜像和容器的数量
docker --help			# 帮助命令
```

## 镜像命令

```shell
docker search 关键字									# 搜索镜像
docker pull 关键字										# 下载镜像
docker images												# 查看已经下载好的镜像
docker rmi 镜像id										 # 删除镜像
docker rmi -f $(docker images -aq)  # 批量删除镜像
```

## 容器命令

### 查看当前运行的容器

```shell
docker ps 				# 查看当前运行的容器
```

### 创建新容器并且启动

```shell
docker run [可选参数] image
# 参数说明
--name="Name" 容器名字 tomcat01,tomcat02 用来区分容器
-d  后台方法运行
-it 使用交互方式运行，进入容器查看内容
-p  宿主机的端口和容器的端口进行绑定 前面是宿主机的端口，后面的是容器的端口
# 举例:		docker run -d -p 91:80 nginx 
# 			 docker run -it centos /bin/bash
```

### 进入某个容器下

```shell
docker exec -it 容器id /bin/bash
```

### 退出容器

```shell
exit    			# 停止容器并且退出
ctrl + p + q  # 容器不停止退出
```

### 删除容器

```shell
docker rm 容器id								# 删除指定的容器，如果要删除正在运行的容器，需要先停止容器
docker rm -f $(docker ps -aq)  # 删除所有容器
```

### 启动和停止容器

```shell
docker exec -it 容器id /bin/bash	# 进入容器
docker start 容器id
docker restart 容器id
docker stop 容器id
docker kill 容器id
```

## 其他常用命令

```shell
docker logs 容器id 											# 查看log
docker inspect 容器id   								# 查看数据元信息
docker cp 容器id:容器内路径 目的的主机路径	# 从容器内拷贝文件到主机上
docker stats													 # 查看DockerCPU,内存情况
```

# 容器数据卷

## 什么是容器数据卷

容器数据卷就是宿主机的文件和容器内的文件进行同步。

挂载好之后，修改宿主机的文件相当于修改了容器内的文件。修改容器内的文件也同样影响宿主机的文件。

## 宿主机与容器进行挂载

```shell
docker run -it -v 宿主机的文件夹目录:容器的目录 镜像名称 /bin/bash
# 举例: docker run -it -v /home/ceshi:/home centos /bin/bash
# 挂载的几种方式
# -v /宿主机路径:容器路径    # 指定路径挂载
# -v 容器内路径					   # 匿名挂载
# -v 卷名:容器内路径				 # 具名挂载
```

# DockerFile

## DockerFile介绍



Dockerfile就是用来构建docker镜像的构建文件

构建步骤:

1. 编写一个dockerfile文件
2. docker build 构建成一个镜像
3. docker run 运行镜像
4. docker push 发布镜像(DockerHub,阿里云镜像仓库)

## DockerFile构建过程



**基础知识：**

1. 每个保留关键字（指令）都是必须大写字母
2. 执行顺序是从上到下执行
3. '#'  代表注释 
4. 每一个指令都会创建提交一个新的镜像层，并且提交

### DockerFile的指令

```shell
FROM					# 基础镜像，一切从这里开始构建
MAINTAINER		# 镜像是谁写的，姓名+邮箱
RUN						# 镜像构建的时候需要运行的命令
ADD						# 添加的内容项
WORKDIR				# 镜像的工作目录
VOLUME				# 挂载的目录
EXPOSE				# 暴露端口配置
CMD						# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD				# 当构建一个被继承 DockerFile 这个时候就会运行 ONBUILD 的执行。是触发指令
COPY					# 类似ADD，将我们的文件拷贝到镜像中
ENV						# 构建的时候设置环境变量
```





# Docker 网络

## 理解Docker0

*首先要清空所有环境*

- 清空所有容器
- 清空所有镜像

>测试 在宿主机上查看ip

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:16:3e:2e:0b:6a brd ff:ff:ff:ff:ff:ff
    inet 172.17.205.202/20 brd 172.17.207.255 scope global dynamic noprefixroute eth0
       valid_lft 315342532sec preferred_lft 315342532sec
    inet6 fe80::216:3eff:fe2e:b6a/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:95:cc:1b:d7 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:95ff:fecc:1bd7/64 scope link
       valid_lft forever preferred_lft forever
# 发现有三个网络
# lo 			本机回环地址
# eth0 		阿里云内网地址
# docker0 docker0 地址

# 创建一个容器
docker run -d -P --name tomcat01 tomcat
# 查看容器内部的网络地址
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
10: eth0@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.2/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# 发现有二个网络       
# lo 						容器的回环地址
# 10 eth0@if11	docker分配的地址

# 再宿主机上ping 172.18.0.2 是可以ping通的
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.
64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.050 ms
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.043 ms
64 bytes from 172.18.0.2: icmp_seq=3 ttl=64 time=0.042 ms
```

> 再次在宿主机上执行ip addr

这时候会发现多了一个地址! 11: vethe20ace9@if10

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:16:3e:2e:0b:6a brd ff:ff:ff:ff:ff:ff
    inet 172.17.205.202/20 brd 172.17.207.255 scope global dynamic noprefixroute eth0
       valid_lft 315341663sec preferred_lft 315341663sec
    inet6 fe80::216:3eff:fe2e:b6a/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:95:cc:1b:d7 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:95ff:fecc:1bd7/64 scope link
       valid_lft forever preferred_lft forever
11: vethe20ace9@if10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 76:b7:f1:11:9e:3f brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::74b7:f1ff:fe11:9e3f/64 scope link
       valid_lft forever preferred_lft forever
# 再创建一个tomcat容器2号
docker run -d -P --name tomcat02 tomcat
```

> 再次在宿主机上执行ip addr

这时候会发现又多了一个地址!  13: vethf1fdcc9@if12

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:16:3e:2e:0b:6a brd ff:ff:ff:ff:ff:ff
    inet 172.17.205.202/20 brd 172.17.207.255 scope global dynamic noprefixroute eth0
       valid_lft 315341106sec preferred_lft 315341106sec
    inet6 fe80::216:3eff:fe2e:b6a/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:95:cc:1b:d7 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:95ff:fecc:1bd7/64 scope link
       valid_lft forever preferred_lft forever
11: vethe20ace9@if10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 76:b7:f1:11:9e:3f brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::74b7:f1ff:fe11:9e3f/64 scope link
       valid_lft forever preferred_lft forever
13: vethf1fdcc9@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether ae:49:c1:8a:7f:f4 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::ac49:c1ff:fe8a:7ff4/64 scope link
       valid_lft forever preferred_lft forever
```

每当启动一个docker容器，docker就会给docker容器分配一个ip,我们只要安装了docker,就会有一个网卡docker0桥接模式，使用的是 veth-pair 技术.

- 我们发现这个容器带来的网卡，都是一对一对的
- veth-pair 就是一对的虚拟设备接口，他们都是成对出现的
- 正因为有这个特征，veth-pair 充当一个桥梁，连接各种虚拟网络设备
- OpenStac,Docke容器之前的连接，OVS的连接，都是使用 veth-pair 技术

> 继续来测试tomcat01 和 tomcat02 是否可以ping通

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker exec -it tomcat02 ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.
64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.076 ms
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.059 ms

# 结论容器之间可以ping通
```

流程:

- tomcat01容器和tomcat02容器都是通过veth-pair连接着docker0
- 通过docker0进行转发
- 即tomcat01---->docker0----->tomcat02

Docker0的问题，不支持容器名连接访问。

## 自定义网络

> 查看所有的docker网络

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5967b4423149        bridge              bridge              local
dff6298e9dac        host                host                local
ffa13682a755        none                null                local
```

**网络模式**

bridge: 桥接模式，默认的模式，自定义网络也使用bridge模式

none: 不配置网络

host: 和宿主机共享网络

container: 容器网络连通(用的少，局限性大)

**测试**

```shell
# 我们直接用下面命令启动容器的时候，会有一个默认的参数 --net bridge,而这个就是我们的docker0
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点，域名不能访问。我们可以自定义一个网络
# --driver bridge 桥接模式,默认也是这个模式，可以不用写
# --subnet 192.168.0.0/16 这个网络支持192.168.0.2-192.168.255.255
# --gateway 网关地址192.168.0.1

[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
071a7e7692ae465e46f54e2b965748b2ca8b05468fd8e530a7f438c47d6883bd

# 再次查看的docker网络会发现我们刚创建的mynet
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5967b4423149        bridge              bridge              local
dff6298e9dac        host                host                local
071a7e7692ae        mynet               bridge              local
ffa13682a755        none                null                local

# 可以查看自定义网络的详细信息
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "071a7e7692ae465e46f54e2b965748b2ca8b05468fd8e530a7f438c47d6883bd",
        "Created": "2020-10-12T15:56:20.264031867+08:00",
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
```

**我们创建2个容器跑到自己创建的网络里面**

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
e4606e360d52289fe7e4c90600ee7429e2eb825ee3abb08ff0055d9ff38cb8e8
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
e84294f85bc7caba102e1e30d0487b2eba446f27fa6de8452f2083c203b15701

# 再次查看自定义网络详细信息，可以发现2个新创建的容器也在里面！
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "071a7e7692ae465e46f54e2b965748b2ca8b05468fd8e530a7f438c47d6883bd",
        "Created": "2020-10-12T15:56:20.264031867+08:00",
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
            "e4606e360d52289fe7e4c90600ee7429e2eb825ee3abb08ff0055d9ff38cb8e8": {
                "Name": "tomcat-net-01",
                "EndpointID": "7a8fccdaecbf4ebdb7f933b4c46d4d57b58ca5a59a412924cdfe346c8c7bd12c",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "e84294f85bc7caba102e1e30d0487b2eba446f27fa6de8452f2083c203b15701": {
                "Name": "tomcat-net-02",
                "EndpointID": "010815326d57580bc71362917f75fc10a9e769aa56334617b8acf6c0adaadc03",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

**通过容器名字ping测试 ，发现直接可以ping通**

```shell
[root@iZ2ze3qhd29s8hz9jy0nlwZ ~]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.067 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.060 ms
```

**好处:** 可以搭建redis集群，和mysql集群的时候，使用自己的网络。互不干扰。保证集群是安全健康的。

##  网络连通

```shell
# 场景：A网络里面的容器想要连接B网络里面的容器.
# 执行 connect命令即可
docker network connect 网络名 容器名
```

# 实战

## Mysql数据挂载到宿主机

```shell
# 1. 去dockerhub查询Mysql版本信息，获取镜像
docker pull mysql:8.0
# 2. 启动容器并且设置密码
docker run -d -p 3306:3306 --name mysql01 -v /home/mydata/mysql/conf:/etc/mysql/conf.d -v /home/mydata/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0
```

## 创建一个自己的centos

```shell
# 1. 创建一个空的文件夹存放DockerFile文件 	mkdir /home/mydata/dockerfile
# 2. 创建DockerFile文件							  	vim mudockerfile-centos								
# 3. 编写DockerFile文件

FROM centos
MAINTAINER cy<380920705@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash

# 4. 构建  docker build -f dockerfile文件路径 -t 镜像名:[tag] .
docker build -f mydockerfile-centos -t mycentos:0.1 .

# 4. 测试运行
docker run -it mycentos:0.1
```

## SpringBoot微服务打包Docker镜像

1. 构架springboot项目
2. 打包应用
3. 编写dockerfile
4. 构建镜像
5. 发布运行

打包

找到 idea maven model Lifecycle package 进行打包即可,打出的包再target目录下

编写dockerfile

```shell
FROM java:8

COPY *.jar /app.jar

CMD ["--server.port=8080"]

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

构建镜像

```shell
# 把打好的jar包和dockerfile文件上传到服务器上
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]# ls
Dockerfile  wandata-student-1.0.jar

# 执行构建命令
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]# docker build -t wandata:1.0 .
Sending build context to Docker daemon  16.55MB
Step 1/5 : FROM java:8
 ---> d23bdf5b1b1b
Step 2/5 : COPY *.jar /app.jar
 ---> 31c4c01f2316
Step 3/5 : CMD ["--server.port=8080"]
 ---> Running in 7cc14850ce2d
Removing intermediate container 7cc14850ce2d
 ---> 8478937dd3e1
Step 4/5 : EXPOSE 8080
 ---> Running in 24b443a580c0
Removing intermediate container 24b443a580c0
 ---> e4eaa5492f06
Step 5/5 : ENTRYPOINT ["java","-jar","/app.jar"]
 ---> Running in bd1c94751d69
Removing intermediate container bd1c94751d69
 ---> 20ab39471d99
Successfully built 20ab39471d99
Successfully tagged wandata:1.0
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]#
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]#
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
wandata             1.0                 20ab39471d99        8 seconds ago       660MB
java                8                   d23bdf5b1b1b        3 years ago         643MB


```

运行镜像

```shell
# 9527是宿主机对外开放的端口
# 8080是容器程序开放的端口
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]# docker run -d -p 9527:8080 wandata:1.0
7960f8179b805ab2c6c036cb4284ebb844f2a2b6c9635a9418ad1ff398126bda
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]#
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]#
[root@iZ2ze3qhd29s8hz9jy0nlwZ idea]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
7960f8179b80        wandata:1.0         "java -jar /app.jar …"   4 seconds ago       Up 3 seconds        0.0.0.0:9527->8080/tcp   flamboyant_herschel
```


---
title: 快速上手Docker
tags: [docker, 教程]
---

![png2](/image/docker.png)

## Docker是什么？

Docker主要是为了解决软件的运行环境问题，当然，主要是Linux软件，举一个简单的例子
小明开发了一个网站，这个网站用到的技术包括Nginx，Python，MySQL，Redis，现在小明需要把这个网站部署上线，那么他至少要在一个服务器上做下面几件事
1、在服务器上安装Nginx，Python，MySQL，Redis软件
2、对机器和软件进行相关的配置设置，例如MySQL的端口号，Redis的端口号等
3、把自己开发的网站代码打包，并且上传到服务器上，然后启动相关服务
以上步骤是非常简化的版本，实际上如果真的要手动部署，可能会遇到更多问题
Docker就是解决上面这些问题的，有了Docker，小明在任何一台服务器上部署自己的软件，只需要简单的一个命令

``` shell
$ docker run my-web-app
```

Docker会自动下载相关镜像，并且自动创建一个完全隔离的环境，然后运行相关的软件，这得益于LInux的底层隔离技术，包括Linux 命名空间、控制组和 UnionFS ，注意：Docker与虚拟机不同，虽然二者有点类似，但是Docker的隔离更轻量级，Docker并不是真正的虚拟机，Docker在底层是共享操作系统内核的，而虚拟机则不共享操作系统内核


## 安装Docker


Linux:

``` shell
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

注意：如果安装了旧版本的Docker，需要使用对应的包管理工具卸载

Windows 10 或 MacOS(不支持黑苹果)：
[安装Docker Desktop](https://docs.docker.com/engine/install/)

当然，如果是带UI的Linux发行版也可以安装Linux版本的Docker Desktop
安装完成后在终端运行：

``` shell
$ docker version
```
如果看到版本信息则代表安装成功

## Docker命令：

Docker CLI的全量命令请参考:[这里](https://docs.docker.com/engine/reference/commandline/docker/)


### 使用docker运行nginx

docker run可以直接运行一个容器，当本地不存在时，docker会从远端仓库拉取，默认的远端仓库是docker官方的docker hub，安装了Docker以后，基本上就不需要手动在服务器上安装任何软件了，假如你想运行一个nginx，那么你可以直接用下面的命令：

``` shell
$ docker run nginx
```

输出：
``` shell
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
a2abf6c4d29d: Pull complete
a9edb18cadd1: Pull complete
589b7251471a: Pull complete
186b1aaa4aa6: Pull complete
b4df32aa5a72: Pull complete
a0bcbecc962e: Pull complete
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/06/26 02:59:53 [notice] 1#1: using the "epoll" event method
2022/06/26 02:59:53 [notice] 1#1: nginx/1.21.5
2022/06/26 02:59:53 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2022/06/26 02:59:53 [notice] 1#1: OS: Linux 4.19.130-boot2docker
2022/06/26 02:59:53 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/06/26 02:59:53 [notice] 1#1: start worker processes
2022/06/26 02:59:53 [notice] 1#1: start worker process 30
```

这样，nginx就直接运行起来了，但是默认没有在后台运行，当按下Ctrl+C软件就退出了，可以使用-d参数，让docker在后台运行该容器

``` shell
$ docker run -d nginx
182214dcd10b23bba2559116d13171745550a5a52c745aa699a895edeb55b7d0
```

docker 输出一串ID，nginx容器就在后台运行了，这个ID可以唯一标识一个容器，当需要输入这个ID时，大部分情况下并不需要输入完整的ID，输入前面几个字符就可以了
使用docker ps 查看正在运行的容器
``` shell
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
182214dcd10b   nginx     "/docker-entrypoint.…"   14 minutes ago   Up 14 minutes   80/tcp    cool_williams
```

可以看到正在运行的容器ID，使用的镜像，创建的时间还有端口号，名字等信息。
使用 stop 命令停止容器
``` shell
$ docker stop 182214dcd10b
$ 182214dcd10b
```

此时 docker ps无法查看该停止的容器
``` shell
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

使用 docker ps -a 可以查看所有状态的容器
``` shell
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED             STATUS                          PORTS     NAMES
182214dcd10b   nginx         "/docker-entrypoint.…"   20 minutes ago      Exited (0) About a minute ago             cool_williams
325b084cc16a   nginx         "/docker-entrypoint.…"   About an hour ago   Exited (0) 20 minutes ago                 priceless_cori
c7e018e16d23   hello-world   "/hello"                 2 hours ago         Exited (0) 2 hours ago                    frosty_euclid
```

注意，此时无法通过外部的浏览器访问这个nginx服务器，因为docker内部的端口并没有暴露出来，可以运行下面的命令进行端口映射

``` shell
$ docker run -d -p 80:80 nginx
```

-p参数把docker内部的80端口映射到本机的80端口，打开浏览器，输入localhost就可以看到Nginx的欢迎页面了。
-v参数可以挂载本地文件目录，一般在运行数据库容器时，需要将数据持久化，否则在容器退出后所有的数据将丢失；例如：

``` shell
$ docker run -d -p 80:80 -v /data/nginx:/data nginx
```

以上命令将本机的/data/nginx目录挂到到容器中的/data目录，如果容器中对/data目录的任何修改都将直接映射到外部磁盘上
docker exec命令可以在指定的容器内部执行命令，分为交互式和非交互式两种方式
非交互式：
``` shell
$ docker exec 182214dcd10b ls -al /var
```

exec后面是docker id，然后紧跟要在容器中运行的命令
交互式
``` shell
$ docker exec -it 182214dcd10b bash
```

-i选项代表使用交互方式，-t代表打开一个终端，bash是本次要运行的命令，一个shell程序
这样就相当于打开了一个交互式终端进入了容器内部，所做的操作都只在容器内部生效，如同进入了一个虚拟机，使用exit命令来退出容器。

## 使用DockerFile创建自己的容器

Docker使用一种称为Dockerfile的文件来描述容器的组成以及相关配置，Dockerfile是docker的特有文件，必须满足相关格式
假如要创建自己的web程序，需要将所有依赖都打包到一个镜像中，以某个操作系统为底座来构筑自己的镜像
作为样例，我们使用python的flask框架来编写一个最简单的网页程序，并打包成一个镜像；关于flask可以参考如下[链接](https://flask.palletsprojects.com/en/2.1.x/)

1、新建一个文件夹
``` shell
mkdir flask
```

2、打开一个py文件app.py，写入如下内容：
``` python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"

if __name__ == "__main__":
     app.run(host="0.0.0.0", debug=True)
```

3、新建一个文件命名为dockerfile，写入如下内容：
``` dockerfile
#这是一个注释
FROM ubuntu

RUN apt-get update
RUN apt-get install -y nginx
RUN apt-get install -y python3
RUN apt-get install -y python3-flask
COPY app.py /var/hello.py
ENV FLASK_APP=hello
CMD cd /var && flask run
```

dockerfile其实只有一种格式，就是
``` dockerfile
# 注释
INSTRUCTION arguments 
```

一个命令然后紧跟参数
dockerfile必须以FROM开始，代表在某个基础镜像上做接下来的操作，一般情况下以某个操作系统或某个现成的软件镜像开始；
其他常用命令包括：
RUN命令可以在镜像内部运行某个命令，本例中是使用apt-get命令来安装必要的软件
COPY命令可以将本地文件拷贝到容器内部，注意只能拷贝当前目录下的内容，因此COPY ../xxx xxx这种形式是无法成功的
ENV命令则可以简单的对容器注入环境变量
CMD命令用于指定当容器运行时需要执行的默认命令，CMD命令可以被docker run命令覆盖，如果不希望被覆盖，可以考虑使用ENTRYPOINT
全部的dockerfile手册请参考[官方文档](https://docs.docker.com/engine/reference/builder/#usage)

之后就可以通过docker buid命令来构建镜像了
4、运行：
``` shell
$ docker build -t flask:latest .
```
得到如下输出：
``` shell
[+] Building 0.2s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                                                   0.0s
 => => transferring dockerfile: 248B                                                                                                   0.0s
 => [internal] load .dockerignore                                                                                                      0.0s
 => => transferring context: 2B                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                                       0.0s
 => [internal] load build context                                                                                                      0.0s
 => => transferring context: 27B                                                                                                       0.0s
 => [1/6] FROM docker.io/library/ubuntu                                                                                                0.0s
 => CACHED [2/6] RUN apt-get update                                                                                                    0.0s
 => CACHED [3/6] RUN apt-get install -y nginx                                                                                          0.0s
 => CACHED [4/6] RUN apt-get install -y python3                                                                                        0.0s
 => [6/6] COPY app.py /var/hello.py                                                                                                    0.0s
 => exporting to image                                                                                                                 0.0s
 => => writing image sha256:8ee4b660822edecb1bbe35f0421d57cfa94fe51533d16737e9ca0c84292fedf8                                           0.0s
 => => naming to docker.io/library/flask:latest                                                                                        0.0s
 ```
-t 参数用于指明镜像的名称和tag，后面紧跟一个目录，docker会自动查找该目录下的dockerfile并根据该文件来进行镜像构建
构建完成后可以使用docker image ls命令来查看当前机器上所有的镜像列表
```  shell
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
flask        latest    8ee4b660822e   5 seconds ago   213MB
<none>       <none>    8b35a6e3e43d   7 minutes ago   213MB
nginx        latest    605c77e624dd   6 months ago    141MB
ubuntu       latest    ba6acccedd29   8 months ago    72.8MB
```
docker image命令是用来管理镜像的，使用方法可以通过:

``` shell
$ docker image --help
Usage:  docker image COMMAND

Manage images

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```

对于任意的docker命令都可以通过 docker COMMAND --help来查看使用方法。
运行一下制作的镜像
``` shell
$ docker run flask
 * Serving Flask app "hello"
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```
至此，镜像已经成功制作并运行

## Docker Registry 镜像仓库

git有github，docker也有docker hub，和git类似，docker需要一个存储所有镜像的地方，这样你可以和别人共享你制作的镜像，也可以使用别人制作的镜像，Docker官方提供的默认仓库是Docker hub，如果要修改默认的仓库可以运行：

``` shell
sudo vim /etc/docker/daemon.json
```

写入：
``` json
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```
列表中可以使用任何提供docker仓库服务的地址，上述例子中使用的是中科大的镜像仓库。
接着，需要使配置生效：

``` shell
sudo systemctl daemon-reload 
sudo service docker restart 
```

当然也可以使用本地仓库，使用本地仓库需要使用docker-registry，docker通过一个镜像提供了该服务，运行：

``` shell
$ docker run -d -p 5000:5000 --restart=always --name registry registry
```
默认情况下，仓库会被创建在容器的 /var/lib/registry 目录下。你可以通过 -v 参数来将镜像文件存放在本地的指定路径。例如下面的例子将上传的镜像放到本地的 /data/registry 目录。

``` shell
$ docker run -d  -p 5000:5000  -v /data/registry:/var/lib/registry registry
```
这样就运行了一个本地仓库并且镜像持久化的存储在/data/registry目录下。
先尝试将本地的镜像推到仓库中：

``` shell
$ docker push 127.0.0.1:5000/ubuntu:latest
The push refers to repository [127.0.0.1:5000/ubuntu]
373a30c24545: Pushed
a9148f5200b0: Pushed
cdd3de0940ab: Pushed
fc56279bbb33: Pushed
b38367233d37: Pushed
2aebd096e0e2: Pushed
latest: digest: sha256:fe4277621f10b5026266932ddf760f5a756d2facd505a94d2da12f4f52f71f5a size: 1568
```

用 curl 查看仓库中的镜像。
``` shell
$ curl 127.0.0.1:5000/v2/_catalog
{"repositories":["ubuntu"]}
```

可以看到 {"repositories":["ubuntu"]}，表明镜像已经被成功上传了。

## Docker compose
我们往往需要多个容器配合来完成某个上层应用，例如网站程序经常需要搭配一个数据库使用，这种情况可以把网站后台程序和数据库分别放到两个容器中运行，Docker compose工具提供一种简化的方式来组织多个容器，并且能够用一个命令快速的部署多个容器；
Docker compose使用YAML文件来描述容器以及容器之间的关系，使用Docker compose一般分为3步
- 定义每个容器的Dockerfile
- 定义一个称为docker-compose.yaml的文件，用来描述多个容器之间的关系
- 使用docker compose up命令一键部署应用程序
我们仍然以上个例子中的flask程序为基础，并新增一个计数功能，为此我们需要改写我们的web程序，如下:

``` python
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
     count = redis.incr('hits')
     return '<p>Hello World! {} times。</p>'.format(count)

if __name__ == "__main__":
     app.run(host="0.0.0.0", debug=True)
```

经过修改后，我们的程序会连接一个Redis数据库，并且每次被访问到，都会将计数加一，并展示到页面，为了连接Redis我们必须运行一个Redis的容器，好在该容器官方都制作好了，因此直接运行
``` shell
$ docker run -d redis
```
就可以直接运行一个Redis数据库，但是此时web程序并没有连接到这个Redis容器，我们需要定义两个容器的关系
编写一个简单的docker-compose.yaml文件

``` yaml
services:
     web:
         build: .
         ports:
             - "5000:5000"
     redis:
         image: "redis:alpine"
```

docker-compose文件必须有一个services字段，每一个service可以理解为一个由多个容器组成的一个上层应用上述例子中的service由两个容器组成，名字分别是web、redis，其中web容器需要使用docker build命令来构筑镜像，使用的目录为当前目录，并且需要把端口5000映射到主机的5000端口
redis容器责直接从系统配置的docker registry中尝试直接获取镜像
运行

``` shell
$ docker compose up
[+] Running 2/2
 - Container nginxpython-redis-1  Started                                                                0.3s
 - Container nginxpython-web-1    Started                                                                0.3s
```

此时两个容器就一并开始构建和启动起来了
这里需要解释一个问题：为什么使用docker compose，flask程序就可以直接连接到一个名字为'redis'的主机上，他又是怎么找到这个主机的呢? 这与docker network的机制有关。

## Docker network
第一次安装完Docker，直接运行

``` shell
$ docker network ls
```

能够直接看到docker建立的默认网络，总共有3个

``` shell
NETWORK ID     NAME      DRIVER    SCOPE
479bda8f37b9   bridge    bridge    local
273213114bfe   host      host      local
cce99c0034c4   none      null      local
```

bridge：docker 默认使用的是bridge，原理上是一个软件实现的二层交换机，如果不使用任何参数运行容器，那么默认会连接到bridge上，也就是说，默认一个机器上的容器都是可以互联互通的，但是这种情况下也只能通过IP来互联互通，无法通过hostname进行交互
host：移除容器和 Docker 宿主机之间的网络隔离，并直接使用主机的网络。host 模式仅适用于 Docker 17.06+
none：对于此容器，禁用所有联网。通常与自定义网络驱动程序一起使用。none 模式不适用于集群服务
在Docker compose章节的例子中，flask程序是直接连接的一个名为'redis'的host，而容器本身并不知道redis这个host对应的ip地址是什么，所以无法连接
如果要解决这个问题，我们可以手动修改flask程序的host文件，配置上redis容器的地址以及host，这样就可以互通了
还有另外一种方法就是使用docker compose命令，如同上个章节那样，这种情况下，docker会默认建立一个自定义网络，而不是使用默认的bridge网络，此外，如果使用自定义网络，docker还会默认提供一个DNS服务器，用于一个services下的各个容器之间的域名解析，在运行完docker compose up后，可以查看docker network ls，会发现docker建立了一个网络，名称为nginxpython_default   
```
$ docker network ls
NETWORK ID     NAME                  DRIVER    SCOPE
add5f8daed77   bridge                bridge    local
3c065d8a1d64   host                  host      local
41afd011cef9   nginxpython_default   bridge    local
c31bc25f91ae   none                  null      local
```
此时，该docker compose定义的两个容器就能够通过镜像名称作为主机名称进行互联，且网络与其他容器是隔离的
docker 网络相关材料请参考[官方文档](https://docs.docker.com/network/)
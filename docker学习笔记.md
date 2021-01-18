# Docker学习基本原理和操作

开始：：

`service docker start`

* 拉取镜像
```powershell
docker pull isoname:latest
docker run --name first_docker_container isoname:latest echo "Hello"
```

1. 查看容器信息
docker ps [-a查看所有容器]
2. 停止一个容器
docker stop [option] Container [Container ...]

3. run [-d 容器在后台运行并且不会将输出结果输出到控制台]

4. 进入docker的几种方法

* ssh登陆
* nsenter、nsinit第三方工具
* **docker本身的工具**

```powershell
docker attach containerId|containerName
1. Id不用输全，只需要标全即可
2. 命令实际上进入容器“启动命令”的终端

docker exec [options] containerName|containerId command [arg]

例子：
docker exec id mkdir dir
docker exec -it id /bin/bash
1. -it为其分配了一个伪终端绑定到标准输出上 

两种方式的比较：
1. attach直接进入容器的启动命令的终端，不会启动新的进程

2. exec会建立新的终端同时可以启动新的进程

使用场景：直接在终端中查看容器的启动命令的输出，使用attach；其他都是用exec
```

5. 删除容器
```powershell
docker rm containName|containId # 删除处于终止状态的容器，没有参数的情况下，只能删除处于终止状态的容器

# 删除正在运行的容器：
# way 1. 停止后删除
docker stop dockerId|dockerName
docker rm dockerId|dockerName

docker rm $(docker ps -a -q) # 删除所有停止状态的容器

# way 2. 强制删除
docker rm -f
```

6. 定制容器
你需要知道：你是无法直接查看一个容器的内容，因为镜像是只读的，也无法做出任何修改。
```powershell
# 实例：修改nginx的欢迎界面
docker run --name webserver -d -p 80:80 nginx
docker exec -it webserver /bin/bash # 进入
echo '<h1>Hello Docer!</h1>' > /usr/share/nginx/html/index.html
```

保存为镜像：在原有的镜像的基础上，再叠加上容器的存储层（存储层仅仅保存了容器所做出的修改），将这些内容构成一个新的镜像
```powershell
docker commit [options:--author作者；--message:指定提交的信息] CONTAINERNAME [isoname新镜像的名字]

docker commit webserver nginx:v1
```

7. 保存和加载镜像
一个或者多个镜像保存至tar文件:`docker save [OPTIONS] IMAGE [IMAGE...]`
```powershell
# 实例
docker save alpine:latest > alpine.tar
# 另一种写法
docker save -o alpine:latest alpine.tar

# 多个 -> 一个
docker save alpine:latest ubuntu:latest > image.tar
```

load加载save保存的tar文件
```powershell
docker load < alpine.tar
# 或者
docker load -i alpine.tar
```

综合案例:将一个镜像从一个机器上打包后发送到另一个机器上并解包
```powershell
docker save [isoname] | bzip2 | pv | ssh username@hostname 'cat | docker load'
```

8. 导入导出容器
容器文件系统>>tar包：`docker export [options] containername`
```powershell
# 实例
docker export container1 > container1.tar
# 或者
docker export container1 -o container1.tar
```

从tar导入一个镜像:`docker import [options]file/url - isoname`
```powershell
# 实例
docker cat container1.tar | docker import - test:v1.0
``` 

关于docker export 和 docker save
1. docker save将一个镜像保存为一个tar包
2. docker export将一个容器快照保存为一个tar包

docker export 导出的容器快照文件将丢弃所有的历史记录和元数据信息，只保存容器当前的快照状态；而另一个将保存所有内容

9. 删除镜像
`docker rmi [options] image [image ...]`
`docker images --digests` 查看镜像的具体信息1
`docker images -q reponame`输出所有仓库名的镜像id
```powershell
docker rmi $(docker images -q reponames)
docker rmi $(docker images -qa)
```

**删除镜像前，需要先将使用该镜像的容器删除**

10. 创建私人仓库
[详细配置](https://docs.docker.com/registry/deploying/#start-the-registry-automatically )
```powershell
docker run -d(后台运行) -p 5000:5000(映射端口，使主机访问容器) --restart=always(docker服务重启或者容器退出时会重新启动) --name registry registry:2
```

11. 镜像送到私人仓库
标记镜像
```powershell
docker tag ubuntu:latest localhost:5000/my-ubuntu
```
推送镜像到仓库
```powershell
docker push localhost:5000/my-ubuntu
```

12. 私人仓库拉取镜像
```powershell
docker pull localhost:5000/my-ubuntu
```

13. 查看、删除私人仓库镜像
使用harbor可视化界面操作
参考：https://github.com/vmware/harbor/

14. 删除私人仓库
```powershell
docker rm [-f强制删除][-v同时删除镜像]
```

15. Dockfile
镜像的定制实际上就是定制每一层所添加的配置、文件。那么如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题就都会解决。这个脚本就是Dockerfile。
```powershell
# 实例
mkdir newdir
cd newdir
touch Dockerfile
#>>>>>>>>>>>>>>创建dockerfile>>>>>>>>
echo "FROM ubuntu:latest" > Dockerfile
echo "RUN mkdir /dir1" >> Dockerfile
#<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
docker build -t testimage .
```

16. docker build 创建
本地构建
URL构建如git(参考：https://docs.docker.com/engine/reference/commandline/build/#tarball-contexts )

> 记录一下：云计算没有讲第七章，云安全相关

17. build、COPY、ADD
镜像的每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。

因而使用ADD来添加tar文件内容而不保留tar文件，别使用COPY(只复制了tar文件)

CMD 与 ENTRYPOINT

实例1,CMD设置的启动命令：无法添加参数
```powershell
# 构建镜像的DOCKFILE
FROM centos
RUN yum install curl
CMD ["curl","-s","http://ip.cn"]
```

实例2,ENTRYPOINT可以解决问题
```powershell
FROM centos
RUN yum install curl
ENTRYPOINT ["curl","-s","http://ip.cn"]
```

ENV：设置环境变量
1. ENV [key] [value]
2. ENV [key]=[value]

EXPOSE [PORT 1][PORT 2 ...]

WORKDIR [工作目录]
为其他指令设置工作目录

ARG [varname][=DEFAULT VALUE]
定义的不是环境变量

使用ENV配置java与tomcat的环境变量，由于tomcat服务会默认监听8080端口，所以使用EXPOSE暴露端口号。最后使用ENTRYPOINT设置启动命令，使tomcat服务随容器启动而启动。
```powershell
FROM ubuntu
WORKDIR /var/tmp
RUN apt-get update && \
    apt-get install -y wget && \
    wget --no-check-certificate --no-cookies --header "Cookie: o\fraclelicense=accept-securebackup-cookie"  http://download.o\fracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jre-8u144-linux-x64.tar.gz && \
    tar -xzf jre-8u144-linux-x64.tar.gz && \
    rm jre-8u144-linux-x64.tar.gz
RUN wget "http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.0.45/bin/apache-tomcat-8.0.45.tar.gz" && \
    tar -xzf apache-tomcat-8.0.45.tar.gz && \
    rm apache-tomcat-8.0.45.tar.gz
ENV JAVA_HOME /var/tmp/jre1.8.0_144
ENV CATALINA_HOME /var/tmp/apache-tomcat-8.0.45
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin
EXPOSE 8080
ENTRYPOINT /var/tmp/apache-tomcat-8.0.45/bin/startup.sh && /bin/bash
```

ONBUILD [其他指令]
添加一个将来执行的触发器
ONBUILD 是一个特殊的指令，它后面跟的是其它指令，比如RUN， COPY等，而这些指令，在当前镜像构建时并不会被执行。只有当以当前镜像为基础镜像，去构建下一级镜像的时候才会被执行。

ONBUILD指令的具体执行步骤
（1）在构建过程中，ONBUILD指令会添加到触发器指令镜像元数据中，这些触发器指令并不会在当前构建过程中执行。
（2）在构建过程后，触发器指令会被存储在镜像详情中，其主键是OnBuild，可以使用docker inspect命令查看。
（3）在之后该镜像可能作为其他Dockerfile中FROM指令的参数。在构建过程中，FROM指令会查找ONBUILD触发器指令，并且会以它们注册的顺序执行。若有触发器指令执行失败，则FROM指令被中止，并返回失败；若所有触发器指令执行成功，则FROM指令完成并继续执行下面的指令。在镜像构建完成后，触发器指令会被清除，不会被子孙镜像继承。

VOLUME定义匿名卷
VOLUME [路径1,路径2 ...]

Docker缓存机制
1. 从已经在缓存中的父镜像开始，将下一个指令与从该基本镜像导出的所有子镜像进行比较，以查看其中一个是否使用完全相同的指令构建。如果没有，则缓存无效；

2. 在大多数情况下，只需将Dockerfile中的指令与其中一个子镜像进行比较即可（通过比较是否与上一次执行的指令一致）。但是，某些指令需要一些额外的检查。对于ADD和COPY指令，将检查镜像中文件的内容，并为每个文件计算校验和。在这些校验和中不考虑文件的最后修改和最后访问的时间。在缓存查找期间，将校验和与现有映像中的校验和进行比较。如果文件（如内容和元数据）中有任何变化，则缓存无效；

3. 除了ADD和COPY命令之外，缓存检查将不会查看容器中的文件来确定缓存匹配。例如，当处理RUN apt-get -y update命令时，不会检查在容器中更新的文件以确定是否存在高速缓存命中，它将只会检查命令字符串是否与之前的一致来判断是否匹配。

4. 一旦某一层的缓存无效，所有后续的Dockerfile命令将生成新的镜像，并且高速缓存将不被使用。
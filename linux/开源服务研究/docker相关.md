##dock的安装

**安装步骤**



增加软件源

	apt-get update
	apt-get install apt-transport-https

	echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list

	apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list
更新软件源
	root@ubuntu14:~# apt-get update

root@ubuntu14:~#  apt-get install  lxc-docker

这个可能不要docker.io

下载依赖包

	wget http://get.docker.io/ubuntu/pool/main/l/lxc-docker-1.0.1/lxc-docker-1.0.1_1.0.1_amd64.deb

安装完成了

执行下面命令验证是否安装成功：

这个是一个交互的容器

	root@ubuntu14:~# docker run -i -t ubuntu /bin/bash

将会下载ubuntu镜像并执行bash
	Type exit to exit

-t标志指定一个容器的伪终端或终端 

-i标志允许我们通过抓住容器中（STDIN）的标准进行交互式连接


* docker ps    列出容器
* docker logs  显示容器的输出
* docker stop   停止容器的运行


查看当前的docker版本

 	$ docker version

当指定一个image名称的时候，会现在本地找，如果找不到就会去网上找，并且下载下来

对于当前的容器，bash shell 进程退出，容器就会停止

现在运行一个命令，然后退出有一定的作用，但是还不是有很大帮助，我们可以创建一个容器，想一个进程一样，像其他一些应用一样

 	docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
 	d7ee56521c905715583b456d0898374772753fe11899e62847c610576104fb8e

运行完了后，会报一串很长的字符串，这个字符串是容器的ID

以后这个ID会缩短，并且可以命名

使用这个命令可以查看我们的容器在做什么

	root@ubuntu14:~# docker ps

	root@ubuntu14:~# docker ps
	CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
	d7ee56521c90        ubuntu:latest       /bin/sh -c 'while tr   4 minutes ago       Up 4 minutes                            hopeful_fermi
   
这个可以看到容器里面真正在做什么事情，名称为上面的命令得到的name 然后使用docker logs 命令来查看

	root@ubuntu14:~# docker logs hopeful_fermi    

停掉那个进程使用命令：

	root@ubuntu14:~# docker stop hopeful_fermi
	hopeful_fermi 

如果执行正确，那么马上会返回容器的名称

使用docker ps 来检查执行的结果正确不

root@ubuntu14:~# docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

使用docker run 可以有两种方式：

* 一种是前端的交换模式
* 一种是后端的进程模式
 
主要三个命令如下：

* docker ps  列出容器
* docker logs 显示容器的输出
* docker stop 停止容器

docker的所有命令：
http://docs.docker.com/reference/commandline/cli/
更多信息
http://docs.docker.com/reference/run/

使用这个命令查看我们的web信息

	docker ps -l

需要映射端口的时候，使用这个命令 -p

 	docker run -d -p 5000:5000 training/webapp python app.py

查看端口映射关系

	sudo docker port nostalgic_morse 5000
	0.0.0.0:49155

实时的打印log信息加上-f 参数

	$ sudo docker logs -f nostalgic_morse


检查我们的容器

root@ubuntu14:~# docker inspect mad_wilson |less

查看指定的容器的参数

	sudo docker inspect -f '{{ .NetworkSettings.IPAddress }}' nostalgic_morse
	172.17.0.5

在停止容器以后，可以选择创建一个新的容器，或者重启旧的容器

删除一个容器

docker rm nostalgic_morse

使用-f 强制删除，或者先停止掉容器


###docker image 相关

这一节，我们将介绍

* 管理镜像
* 创建基本的镜像
* 上传镜像


列出本地的镜像

	root@ubuntu14:~# docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	ubuntu              latest              ef83896b7fb9        5 days ago          192.7 MB

可以看到的信息
	
* 镜像的分支
* 镜像的标签
* 镜像的ID

一个分支可以包含很多的镜像

镜像名称后面加标签的时候会进入相应的系统，没加标签的时候，会进入匹配的最后一个系统

建议使用的时候都加上标签，这样能够清楚的知道自己在使用哪个镜像

创建一个镜像的时候，会自动去网上下载镜像，这个时候我们可以提前将镜像下载下来 使用 `docker pull` 命令 

在layer下载下来以后，我们直接运行的时候就可以不用下载直接运行了.

###查找image

我们可以通过web来查找image，或者通过 `docker search` 来查找

	$ sudo docker search sinatra
	NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
	training/sinatra                       Sinatra training image                          0                    [OK]
	marceldegraaf/sinatra                  Sinatra test app                                0
	mattwarren/docker-sinatra-demo                                                         0                    [OK]
	luisbebop/docker-sinatra-hello-world                                                   0                    [OK]
	bmorearty/handson-sinatra              handson-ruby + Sinatra for Hands on with D...   0
	subwiz/sinatra                                                                         0
	bmorearty/sinatra                                                                      0
	. . .

我们可以看到很多的镜像列表

###下载image

我们选择需要的image，我们可以使用 `docker pull` 命令来进行下载

	docker pull  training/sinatra

下载完成后，我们可以运行我们的容器了

	$ sudo docker run -t -i training/sinatra /bin/bash
	root@a8cb6ce02d85:/#

###创建我们自己的image
我们发现有的镜像对我们很有用，但是我们需要对它做一些修改，有两个方法：

* 我们可以更新一个容器，并将更改保存到镜像
* 我们可以指定一个`dockerfile`来创建镜像

更新和提交一个镜像

要更新一个镜像，我们首先需要创建一个容器

	$ sudo docker run -t -i training/sinatra /bin/bash
	@02616b0e5a8:/#

> 注意：容器的ID已经被创建02616b0e5a8，我们后面会使用它

在我们的容器当中，我们增加一个`json` gem

	root@0b2616b0e5a8:/# gem install json

完成以后，我们使用exit退出

现在我们有了一个已经修改了的容器，我们能够使用`docker commit`将容器的内容保存到镜像当中.

	$ sudo docker commit -m="Added json gem" -a="Kate Smith" \
	0b2616b0e5a8 ouruser/sinatra:v2
	4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c

这里，我们使用了 `docker commit` 命令，我们指定了两个标签： `-m` 和 `-a`,`-m`是我们写的提交的相关信息，-a 指定我们容器的更新的作者

我们还指定了容器的ID，以及指定的镜像的目的地：

	ouruser/sinatra:v2


新的镜像包含了新的作者，使用了原来的分支，加了一个新的标签`v2`.

我们可以使用`docker images` 命令查看我们新的镜像

	$ sudo docker images
	REPOSITORY          TAG     IMAGE ID       CREATED       VIRTUAL SIZE
	training/sinatra    latest  5bc342fa0b91   10 hours ago  446.7 MB
	ouruser/sinatra     v2      3c59e02ddd1a   10 hours ago  446.7 MB
	ouruser/sinatra     latest  5db5f8471261   10 hours ago  446.7 MB


###从Dockerfile创建一个新的镜像

使用`docker commit`命令很容易改变一个镜像，但是对于团队的分享并不容易，我们可以使用一个新命令，`docker build`，从头开始创建一个新的镜像

我们需要创建一个`docker file` 包含一系列的说明，告诉docker怎么创建我们的镜像

我们先创建一个`dockerfile`.

	$ mkdir sinatra
	$ cd sinatra
	$ touch Dockerfile

每一个说明都创建一个新的layer，我们来看一个简单的例子

	# This is a comment
	FROM ubuntu:14.04
	MAINTAINER Kate Smith <ksmith@example.com>
	RUN apt-get -qq update
	RUN apt-get -qqy install ruby ruby-dev
	RUN gem install sinatra

每一行前缀都是大写

	INSTRUCTION statement

第一行的FROM告诉我们Docker 我们的image的源是哪里，在这个例子里面，我们使用ubuntu 14.04镜像

接下来，我们使用`MAINTAINER`来指定谁持有这个image

最后我们指定三个 `RUN`命令，我们来安装软件包

还有更多关于dockerfile的

https://docs.docker.com/reference/builder

现在我们使用我们的Dockerfile，是用 docker build 命令来创建一个镜像

	$ sudo docker build -t="ouruser/sinatra:v2" .
	Uploading context  2.56 kB
	Uploading context
	Step 0 : FROM ubuntu:14.04
	 ---> 99ec81b80c55
	Step 1 : MAINTAINER Kate Smith <ksmith@example.com>
	 ---> Running in 7c5664a8a0c1
	 ---> 2fa8ca4e2a13
	Removing intermediate container 7c5664a8a0c1
	Step 2 : RUN apt-get -qq update
	 ---> Running in b07cc3fb4256
	 ---> 50d21070ec0c
	Removing intermediate container b07cc3fb4256
	Step 3 : RUN apt-get -qqy install ruby ruby-dev
	 ---> Running in a5b038dd127e
	Selecting previously unselected package libasan0:amd64.
	(Reading database ... 11518 files and directories currently installed.)
	Preparing to unpack .../libasan0_4.8.2-19ubuntu1_amd64.deb ...
	. . .
	Setting up ruby (1:1.9.3.4) ...
	Setting up ruby1.9.1 (1.9.3.484-2ubuntu1) ...
	Processing triggers for libc-bin (2.19-0ubuntu6) ...
	 ---> 2acb20f17878
	Removing intermediate container a5b038dd127e
	Step 4 : RUN gem install sinatra
	 ---> Running in 5e9d0065c1f7
	. . .
	Successfully installed rack-protection-1.5.3
	Successfully installed sinatra-1.4.5
	4 gems installed
	 ---> 324104cde6ad
	Removing intermediate container 5e9d0065c1f7
	Successfully built 324104cde6ad


我们使用`docker build`命令，使用 `-t`标签来指定我们的所有者，还有版本号

最后我们使用了 `.` 来指定我们的dockerfile在当前目录


###给当前的镜像打个标签

可以使用`docker tag`命令增加标签

	$ sudo docker tag 5db5f8471261 ouruser/sinatra:devel

推送一个镜像到docker hub

一旦你创建完成了镜像，使用`docker push` 命令来提交

##从主机上删除一个镜像

我们使用`docker rmi` 命令来删除一个镜像

我们需要删除`training/sinatra`.


	$ sudo docker rmi training/sinatra
	Untagged: training/sinatra:latest
	Deleted: 5bc342fa0b91cabf65246837015197eecfa24b2213ed6a51a8974ae250fedd8d
	Deleted: ed0fffdcdae5eb2c3a55549857a8be7fc8bc4241fb19ad714364cbfd7a56b22f
	Deleted: 5c58979d73ae448df5af1d8142436d81116187a7633082650549c52c3a2418f0

> 删除一个镜像的时候确保没有容器在使用它


###将容器连接在一起

我们之前使用映射的端口与容器中的服务进行连接，我们这节将介绍新的连接方式

##回顾端口映射

我们运行一个网络应用

	$ sudo docker run -d -P training/webapp python app.py
> 注意：容器有一个独立的网络和ip地址（使用`docker inspect`命令显示容器的IP），Docker可以有很多网络配置，我们可以查看更多信息：
https://docs.docker.com/articles/networking/

当我们使用 `-P`标签来自动映射网络，使用49000到49900之间的随机数，当我们使用 `docker ps`可以看到映射的关系

	$ sudo docker ps nostalgic_morse
	CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
	bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse

我们同样可以使用`-p` 参数进行绑定.

	$ sudo docker run -d -p 5000:5000 training/webapp python app.py

这样做就端口只能给一个容器使用了

	$ sudo docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
 
这样我们可以把端口绑定到指定的网卡上了
 
或者我们把5000端口动态的绑定到localhost

	$ sudo docker run -d -p 127.0.0.1::5000 training/webapp python app.py

使用/udp 绑定udp端口

	$ sudo docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py

使用docker port 进行查询

	$ docker port nostalgic_morse 5000
	127.0.0.1:49155

> -p 可以指定多个端口

###Docker的容器链接

端口映射不是容器链接的唯一方法，docker有一个链接系统，可以相互间链接，有一个父容器

###容器命令
容器链接依赖于容器的名称，我们看到了创建容器的时候，自己生成了随机名称，我们可以自定义名称，这样有两个好处：

* 1、可以让你知道这个容器是做什么的，例如，命名为`web`
* 2、可以让指定的容器进行连接，例如将容器`web`与容器`db`进行连接

可以使用 --name来指定容器的名称

	$ sudo docker run -d -P --name web training/webapp python app.py

	$ sudo docker ps -l
	CONTAINER ID  IMAGE                  COMMAND        CREATED       STATUS       PORTS                    NAMES
	aed84ee21bde  training/webapp:latest python app.py  12 hours ago  Up 2 seconds 0.0.0.0:49154->5000/tcp  web

我们可以使用 docker inspect 返回容器的名称

	$ sudo docker inspect -f "{{ .Name }}" aed84ee21bde
	/web

> 容器的名称是唯参数一的，加入 --rm 参数，那么docker run 命令将会在停止容器后将容器删除

我们创建一个db 容器 

	$ sudo docker run -d --name db training/postgres

我们再创建一个新的容器并且将它链接到db容器

$ sudo docker run -d -P --name web --link db:db training/webapp python app.py

--link的语法
--link name:alias

一个容器可以被多个web链接



###管理容器的数据

我们已经知道基本的容器的操作，这节将介绍怎么在容器之间处理数据

* 数据卷
* 数据卷容器

###数据卷

数据卷是使用union文件系统提供给一个或者多个容器使用的目录

* 数据卷可以被容器共享或者复用
* 数据卷的改变是马上发生的
* 当你更新镜像的时候，卷不会改变
* 卷一致存在，直到没人使用

###增加一个数据卷

你可以使用`-v`参数来增加卷，并且可以多次使用，挂载单一的卷.

	$ sudo docker run -d -P --name web -v /webapp training/webapp python app.py

将会创建一个新的卷 /webapp.

> 可以在dockerfile当中指定一个或者多个卷

挂载主机的目录作为一个数据卷

使用`-v`参数可以挂载一个主机的目录到容器当中去

	$ sudo docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py

这个会将本地的目录挂载到容器当中去，/src/webapp:目录挂载为/opt/webapp,我们可以把本地代码挂载到容器当中去，这个容器中的目录必须指定，如果没有指定，那么容器会自动创建

> 目前这个还不支持在dockerfile当中指定，因为主机是会经常变化的

默认是会创建读写的卷，也可指定一个只读的卷

	$ sudo docker run -d -P --name web -v /src/webapp:/opt/webapp:ro training/webapp python app.py

我们使用ro参数来将卷挂成只读的卷

创建和挂载一个数据卷容器

如果你有数据是需要在容器之间共享的，或者不需要持续的数据，那么最好可以创建一个命名的数据卷容器，然后挂载它

我们创建一个共享卷的容器

	$ sudo docker run -d -v /dbdata --name dbdata training/postgres

可以使用 --volume-from 参数来挂载容器的卷

	$ sudo docker run -d --volumes-from dbdata --name db1 training/postgres
另外一个
	$ sudo docker run -d --volumes-from dbdata --name db2 training/postgres

可以使用多个 -volumes-from 来挂载多个容器当中的卷

	$ sudo docker run -d --name db3 --volumes-from db1 training/postgres

如果要删除一个挂载了容器的卷，包括 dbdata卷，或者子卷 db1和db2,卷不会被删除，直到没有卷引用它，这个允许你升级，或者迁移卷中的数据

###备份，还原，迁移卷
另外一个有用的功能是备份还原和迁移，我们使用--volume-from来创建一个新容器

	$ sudo docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata

这里我们载入了一个新容器，并且从dbdata容器当中挂载卷，然后我们挂载一个本地的/backup，我们备份dbdata的内容到backup.tar





















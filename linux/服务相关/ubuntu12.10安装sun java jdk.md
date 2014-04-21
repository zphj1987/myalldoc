执行命令如下：

> add-apt-repository ppa:webupd8team/java  
apt-get update  
apt-get install oracle-java6-installer   
 
检查java的版本： 
> root@kangear:/etc/apt# java -version

	java version "1.6.0_41"
	Java(TM) SE Runtime Environment (build 1.6.0_41-b02)
	Java HotSpot(TM) Server VM (build 20.14-b01, mixed mode)

 
 
说明：**sun-java6-jre，sun-java6-plugin sun-java6-fonts** 都同样的由软件包 **oracle-java6-installer** 提供的虚拟软件包,所以安装 **oracle-java6-installer**就可以了
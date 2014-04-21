##centos下多网卡做bond以及脚本

多网卡或者单网卡形式下的网卡bonding

获取当前的网卡数量：

> lspci | grep Ethernet | wc -l

假设是两个分别为 **eth0**  和 **eth1** 那么输出的数值为 **2**

centos的网卡配置文件路径在 **/etc/sysconfig/network-scripts/**

需要创建一个新文件  ```ifcfg-bond0```

在这个 **/etc/sysconfig/network-scripts/ifcfg-bond0** 文件下面添加如下：

	DEVICE=bond0	
	ONBOOT=yes
	IPADDR=192.168.3.104
	NETMASK=255.255.0.0
	GATEWAY=192.168.1.1
	BOOTPROTO=static
	USERCTL=no
	ifenslave bond0
	
将网卡配置文件 **/etc/sysconfig/network-scripts/ifcfg-eth0** 修改如下：

	BOOTPROTO=none
	DEVICE=eth0
	ONBOOT=yes
	MASTER=bond0
	USERCTL=no
	SLAVE=yes
将网卡配置文件 **/etc/sysconfig/network-scripts/ifcfg-eth1** 修改如下：

	BOOTPROTO=none
	DEVICE=eth1
	ONBOOT=yes
	MASTER=bond0
	USERCTL=no
	SLAVE=yes

在配置文件 **/etc/modprobe.d/modprobe.conf** 中（没有就创建）添加如下：

	alias bond0 bonding
	options bond0 miimon=100 mode=balance-rr

执行:
> modprobe bonding


在 **/etc/rc.local** 中添加(建议放在最上面一面影响其他网络服务)：

	ifenslave bond0 eth0 eth1

重启网卡：

>  /etc/init.d/network   restart


**配置完成！**

----

有一个脚本方便配置bond，前提是网卡需要是 eth 开头的，脚本如下：

	#! /bin/sh
	#获取当前网卡数
	ethnum=`lspci | grep Ethernet | wc -l`
	echo $ethnum
	#如果网卡数小于等于1则什么都不做
	if [ $ethnum -le 1 ]
	        then
	        echo "do nothing!"
	fi
	#如果网卡数大于等于2则
	if [ $ethnum -ge 2 ]
	        the
	 
	#-------  添加一个bond0的网卡
	    echo "DEVICE=bond0" > /etc/sysconfig/network-scripts/ifcfg-bond0
	    echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/ifcfg-bond0
	    echo "IPADDR=192.168.3.104" >> /etc/sysconfig/network-scripts/ifcfg-bond0
	    echo "NETMASK=255.255.0.0" >> /etc/sysconfig/network-scripts/ifcfg-bond0
	    echo "GATEWAY=192.168.1.1" >> /etc/sysconfig/network-scripts/ifcfg-bond0
	    echo "BOOTPROTO=static" >> /etc/sysconfig/network-scripts/ifcfg-bond0
	    echo "USERCTL=no" >> /etc/sysconfig/network-scripts/ifcfg-bond0
	#--------
	    echo "ifenslave bond0" >> /etc/rc.local
	#--------写其他网卡的配置文件
	    for i in $(seq $ethnum);
	    do
	num=`expr $i - 1`
	     echo "BOOTPROTO=none" >  /etc/sysconfig/network-scripts/ifcfg-eth$num
	     echo "DEVICE=eth$num" >>  /etc/sysconfig/network-scripts/ifcfg-eth$num
	     echo "ONBOOT=yes" >>  /etc/sysconfig/network-scripts/ifcfg-eth$num
	     echo "MASTER=bond0" >>  /etc/sysconfig/network-scripts/ifcfg-eth$num
	     echo "USERCTL=no" >>  /etc/sysconfig/network-scripts/ifcfg-eth$num
	     echo "SLAVE=yes" >>  /etc/sysconfig/network-scripts/ifcfg-eth$num
	     sed -i 's/ifenslave.*/& eth'"$num"'/g' /etc/rc.local
	    done;
	echo "alias bond0 bonding" > /etc/modprobe.d/modprobe.conf
	echo "options bond0 miimon=100 mode=balance-rr" >> /etc/modprobe.d/modprobe.conf
	        modprobe bonding
	    /etc/init.d/network   restart
	fi
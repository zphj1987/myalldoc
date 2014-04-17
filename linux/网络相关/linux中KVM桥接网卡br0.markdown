#linux中KVM桥接网卡br0

在centos虚拟化当中需要增加一个桥接网卡，然后将虚拟化当中的机器的网卡桥接到桥接网卡，下面将描述设置方法：

查看现有网卡
示例：
> [root@zb ~]# vim /etc/sysconfig/network-scripts/ifcfg-eth0

如下

		HWADDR=70:71:BC:1F:E4:86
		TYPE=Ethernet
		UUID=0213211a-d451-4944-881c-e7475b8bee34
		ONBOOT=yes
		NM_CONTROLLED=yes
		BOOTPROTO=static
		IPADDR=192.168.7.107
		NETMASK=255.255.0.0
		GATEWAY=192.168.1.1

将网卡信息copy一份到 **br0** 再进行修改
修改**eth0**的信息为：

		DEVICE=eth0
		HWADDR=70:71:BC:1F:E4:86
		TYPE=Ethernet
		ONBOOT=yes
		NM_CONTROLLED=yes
		BOOTPROTO=static
		BRIDGE=br0     # 新增加了这一句：重要
		修改br0的信息为：
		DEVICE=br0     # 更改eth0为br0
		HWADDR=70:71:BC:1F:E4:86
		TYPE=Bridge       #修改类型为bridge
		ONBOOT=yes
		BOOTPROTO=static
		IPADDR=192.168.7.107
		NETMASK=255.255.0.0
		GATEWAY=192.168.1.1

修改完成后，**eth0**实际是没有ip的，**br0**为当前主机的ip了
 
 
**ubuntu**下的配置方式
 
	auto lo
	iface lo inet loopback
	auto br0
	iface br0 inet static
	bridge_ports eth0
	bridge_stp off
	bridge_maxwait 0
	bridge_fd 0
	address 192.168.5.108
	netmask 255.255.0.0
	gateway 192.168.1.1
	dns-nameservers 192.168.1.1

配置完成后：

![tu](http://fmn.rrimg.com/fmn062/20140121/1420/b_large_UeNP_5d1e000106101262.jpg)

![tu](https://github.com/zphj1987/myalldoc/blob/master/picture/jietu.jpg)

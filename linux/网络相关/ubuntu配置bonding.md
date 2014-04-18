##ubuntu配置单bonding

	auto lo
	iface lo inet loopback

	auto eth6
	iface eth6 inet manual
	bond-master bond0
	 
	auto eth5
	iface eth5 inet manual
	bond-master bond0
	 
	auto eth0
	iface eth0 inet static
	address 192.168.8.102
	netmask 255.255.0.0
	gateway 192.168.1.1
	dns-nameservers 8.8.8.8
	 
	auto bond0
	iface bond0 inet static
	address 10.0.0.102
	netmask 255.255.0.0
	 
	bond-slaves none
	bond-miimon 100
	bond-mode balance-alb
 
 
 
## 配置双bond
	auto lo
	iface lo inet loopback
	 
	auto eth4
	iface eth4 inet manual
	bond-master bond0
	 
	auto eth0
	iface eth0 inet static
	address 10.0.0.103
	netmask 255.255.0.0
	 
	auto bond0
	iface bond0 inet static
	address 192.168.9.103
	netmask 255.255.0.0
	gateway 192.168.1.1
	dns-nameservers 8.8.8.8
	bond-slaves none
	bond-miimon 100
	bond-mode balance-alb
	 	 
	auto eth6
	iface eth6 inet manual
	bond-master bond1
	 
	auto eth7
	iface eth7 inet manual
	bond-master bond1
	 
	auto bond1
	iface bond1 inet static
	address 10.1.0.103
	netmask 255.255.0.0
	bond-slaves none
	bond-miimon 100
	bond-mode balance-alb

---
>文档编写人：张鹏  
	
>有问题联系： 199383004@qq.com
	
>转发请标明转自：
	 	https://github.com/zphj1987/myalldoc
	
>请我喝杯咖啡点击：	[支付宝支持](https://me.alipay.com/zphj1987)

---
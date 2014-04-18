修改同步服务器的配置文件:

 > **vim /etc/ntp.conf** 

删除所有的内容，添加:

	restrict default nomodify
	server  127.127.1.0     # local clock
	fudge   127.127.1.0 stratum 8
重启ntpd：

> **/etc/init.d/ntpd restart**

等待ntp服务器自身同步完成:

> **watch ntpq -p**

![](https://farm8.staticflickr.com/7165/13897272732_6976af2c85_o.jpg)

观察这个 **reach** 需要超过 **17**

否则其他机器 **ntpdate** 同步的时候会报错

> **ntp date 192.168.3.104 **

![](https://farm8.staticflickr.com/7006/13920380035_f9981d45f8_o.jpg)

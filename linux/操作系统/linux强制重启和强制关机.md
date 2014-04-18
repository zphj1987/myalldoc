###强制重启的脚本:

	#! /bin/sh
	echo 1 > /proc/sys/kernel/sysrq
	echo o > /proc/sysrq-trigger

###强制关机的脚本:

	#! /bin/sh
	echo 1 > /proc/sys/kernel/sysrq
	echo b > /proc/sysrq-trigger

---
>文档编写人：张鹏  
	
>有问题联系： 199383004@qq.com
	
>转发请标明转自：
	 	https://github.com/zphj1987/myalldoc
	
>请我喝杯咖啡点击：	[支付宝支持](https://me.alipay.com/zphj1987)

---


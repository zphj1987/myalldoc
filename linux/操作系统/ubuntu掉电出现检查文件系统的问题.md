解决方法如下

修改：	/etc/default/rcS
 
	FSCKFIX=no 为 FSCKFIX=yes

出现这个情况的原因是硬件**时钟偏移**了
 
显示上次挂载根目录在未来时间。

---
>文档编写人：张鹏  
	
>有问题联系： 199383004@qq.com
	
>转发请标明转自：
	 	https://github.com/zphj1987/myalldoc
	
>请我喝杯咖啡点击：	[支付宝支持](https://me.alipay.com/zphj1987)

---
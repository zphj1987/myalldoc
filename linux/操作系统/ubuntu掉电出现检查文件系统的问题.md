解决方法如下

修改：	/etc/default/rcS
 
	FSCKFIX=no 为 FSCKFIX=yes

出现这个情况的原因是硬件**时钟偏移**了
 
显示上次挂载根目录在未来时间。
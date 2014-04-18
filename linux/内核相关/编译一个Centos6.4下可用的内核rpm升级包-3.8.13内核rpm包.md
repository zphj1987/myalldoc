 
在Centos6.4下进行内核升级，采用内核源码的升级方式比较简单，但是需要升级的机器多的情况下进行内核升级就比较麻烦，并且编译内核的速度依赖于机器的性能，一般需要20分钟，而通过rpm内核包的方式进行安装，就比较快，一般在几分钟之内就可以完成内核的安装，本文档以3.8.13内核源码为例子进行的操作。

  根据网上的指导教程编译的rpm包，编译完成后只会生成如下两个rpm包：

* l  kernel-3.8.13-1.x86_64.rpm
* l  kernel-headers-3.8.13-1.x86_64.rpm

而实际进行内核升级的时候还需要**kernel-firmwsare-3.8.13-1.x86_64.rpm** 这个包

这个包是包含内核固件相关文件的，而在进行rpm打包的时候，这个包会内嵌 **kernel-3.8.13-1.x86_64.rpm**这个包当中去，而安装的时候系统没有找到这个包，进行升级的时候就会报错。

所以需要对内核源码编译文件进行一定的修改，这个地方需要修改的文件为**linux-3.8.13/scripts/package/mkspec**	路径下的这个文件，具体修改如下：

* 加入kernel-devel支持需要修改

	    scripts/package/mkspec |   26 ++++++++++++++++++++++++++
		 1 file changed, 26 insertions(+)
		diff --git a/scripts/package/mkspec b/scripts/package/mkspec
		index 514aeb2..65131df 100755
		--- a/scripts/package/mkspec
		+++ b/scripts/package/mkspec
		@@ -59,6 +59,14 @@ echo "header files define structures and constants that are needed for"
		 echo "building most standard programs and are also needed for rebuilding the"
		 echo "glibc package."
		 echo ""
		+echo "%package devel"
		+echo "Summary: Development package for building kernel modules to match the $__KERNELRELEASE kernel"
		+echo "Group: System Environment/Kernel"
		+echo "AutoReqProv: no"
		+echo "%description -n kernel-devel"
		+echo "This package provides kernel headers and makefiles sufficient to build modules"
		+echo "against the $__KERNELRELEASE kernel package."
		+echo ""
		 
		 if ! $PREBUILT; then
		 echo "%prep"
		@@ -109,6 +117,11 @@ echo 'mv vmlinux.bz2 $RPM_BUILD_ROOT'"/boot/vmlinux-$KERNELRELEASE.bz2"
		 echo 'mv vmlinux.orig vmlinux'
		 echo "%endif"
		 
		+echo 'rm -rf $RPM_BUILD_ROOT'"/lib/modules/$KERNELRELEASE/{build,source}"
		+echo "mkdir -p "'$RPM_BUILD_ROOT'"/usr/src/kernels/$KERNELRELEASE"
		+echo "EXCLUDES=\"--exclude-vcs --exclude .tmp_versions --exclude=*vmlinux* --exclude=*.o --exclude=*.ko --exclude=*.cmd --exclude=Documentation --exclude=firmware --exclude .config.old --exclude .missing-syscalls.d\""
		+echo "tar "'$EXCLUDES'" -cf- . | (cd "'$RPM_BUILD_ROOT'"/usr/src/kernels/$KERNELRELEASE;tar xvf -)"
		+
		 echo ""
		 echo "%clean"
		 echo 'rm -rf $RPM_BUILD_ROOT'
		@@ -122,6 +135,15 @@ echo "/sbin/installkernel $KERNELRELEASE /boot/vmlinuz-$KERNELRELEASE-rpm /boot/
		 echo "rm -f /boot/vmlinuz-$KERNELRELEASE-rpm /boot/System.map-$KERNELRELEASE-rpm"
		 echo "fi"
		 echo ""
		+echo "%post devel"
		+echo "cd /lib/modules/$KERNELRELEASE"
		+echo "ln -sf /usr/src/kernels/$KERNELRELEASE build"
		+echo "ln -sf /usr/src/kernels/$KERNELRELEASE source"
		+echo ""
		+echo "%postun devel"
		+echo "cd /lib/modules/$KERNELRELEASE"
		+echo "rm -f build source"
		+echo ""
		 echo "%files"
		 echo '%defattr (-, root, root)'
		 echo "%dir /lib/modules"
		@@ -133,3 +155,7 @@ echo "%files headers"
		 echo '%defattr (-, root, root)'
		 echo "/usr/include"
		 echo ""
		+echo "%files devel"
		+echo '%defattr (-, root, root)'
		+echo "/usr/src/kernels/$KERNELRELEASE"
		+echo ""


* 加入kernel-firmware支持需要修改：
		1 files changed, 11 insertions(+), 1 deletions(-) 
		
		diff --git a/scripts/package/mkspec b/scripts/package/mkspec 
		index b20bdac..35e68d1 100755 
		--- a/scripts/package/mkspec 
		+++ b/scripts/package/mkspec 
		@@ -68,6 +68,13 @@ echo "between the Linux kernel and userspace libraries and programs. The" 
		echo "header files define structures and constants that are needed for" 
		echo "building most standard programs and are also needed for rebuilding the" 
		echo "glibc package." 
		+echo "" 
		+echo "%package firmware" 
		+echo "Summary: Set of firmware images in the kernel tree" 
		+echo "Group: Development/System" 
		+echo "%description firmware" 
		+echo "Firmware images in the kernel tree provided for backward compability" 
		+echo ""
		
		if ! $PREBUILT; then 
		echo "%prep" 
		@@ -137,7 +144,6 @@ echo "%files" 
		echo '%defattr (-, root, root)' 
		echo "%dir /lib/modules" 
		echo "/lib/modules/$KERNELRELEASE" 
		-echo "/lib/firmware" 
		echo "/boot/*" 
		echo "" 
		echo "%files devel" 
		@@ -148,3 +154,7 @@ echo "%files headers" 
		echo '%defattr (-, root, root)' 
		echo "/usr/include" 
		echo "" 
		+echo "%files firmware" 
		+echo '%defattr (-, root, root)' 
		+echo "/lib/firmware" 
		+echo ""

	修改完上面的然后在根目录进行 ```make rpm```  即可生成相应的内核rpm包

	这个是修改完的内核 **mkspec** 文件，可以直接使用:
 
		https://www.dropbox.com/s/nsvyf607t91k2mq/mkspec


	---
	>文档编写人：张鹏  
		
	>有问题联系： 199383004@qq.com
		
	>转发请标明转自：
		 	https://github.com/zphj1987/myalldoc
		
	>请我喝杯咖啡点击：	[支付宝支持](https://me.alipay.com/zphj1987)
	
	---
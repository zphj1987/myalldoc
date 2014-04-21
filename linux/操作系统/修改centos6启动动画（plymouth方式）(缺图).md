**centos6** 默认的启动动画是一个白蓝色的进度条，背景全黑色，现在需要对 **centos** 的启动动画进行定制

在查询了一些资料以后，发现有一个软件是可以对启动动画进行定制的，名字叫 **plymouth**

这个在 **centos6** 操作系统安装完成以后默认就带了的，主要涉及的是如下的几个操作：

列出当前所有主题：

> plymouth-set-default-theme --list  

主题存放目录：

**/usr/share/plymouth/themes/**

不重启查看主题运行效果 Ctrl + Alt + F2（一定不要在F1操作）

> plymouthd
>plymouth --show-splash
>plymouth --quit

如果想调试：

>plymouthd --debug --debug-file=/tmp/plydebug

>plymouth --show-splash

>plymouth --quit

如果在虚拟机下进行测试
需要修改grub里面的vga参数为785

这里缺一个图：

其他环境无需设置

测试通过以后，进行设置：

> plymouth-set-default-theme details              (后面的detail为设置的主题）

> plymouth-set-default-theme -R details         (本操作会将启动画面加入到内核当中，时间比较久，可以ps -ef 查看后台操作)
> 
重启电脑就可以看到效果了

下面是在github上找到的一个源码包，可以使用

[https://www.dropbox.com/s/v6y9igak0e5pj9q/wheat.zip](https://www.dropbox.com/s/v6y9igak0e5pj9q/wheat.zip)

centos下使用自编译主题script形式需要安装依赖包:

**plymouth-graphics-libs-0.8.3-27.el6.centos.x86_64.rpm**

**plymouth-plugin-script-0.8.3-27.el6.centos.x86_64.rpm**

安装完成后，将 **wheat** 解压后目录放在 **/usr/share/plymouth/themes/** 下到就可以设置这个主题了


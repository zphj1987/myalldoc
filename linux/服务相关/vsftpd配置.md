##关于vsftp在ubuntu下面的配置

默认安装完成以后，使用root进行登陆是无法登陆的，这个可能跟ubuntu下默认就禁止了root用户有关，做了一个测试，创建了zbkc用户，然后设置密码，使用zbkc用户进行ftp的登陆就默认登陆到了zbkc的home用户下面

用默认用户建立登陆后是没有写的权限的，有读的权限的


####下面是关于ubuntu下的vsftpd的配置，以及与集群相关的配置

配置vsftpd匿名用户访问权限方面有两个规定：

1、匿名用户根目录的权限不能是ftp可写或者其他用户可写，即父目录权限不要设置成属于ftp用户，并且权限设置成755

2、子目录需要是ftp可写或者其他用户可写（配置匿名可写环境用到）

那么配置一个可供匿名用户下载/zbkc/ftp/ 的配置即为(开启匿名)：

	anonymous_enable=YES 

配置 `/zbkc/ftp` 目录权限为 ` root:root`，读写权限 `755`

在配置文件`/etc/vsftpd.conf `添加（配置匿名用户的根目录）

	anon_root=/zbkc/ftp1

配置完成即为匿名可读的vsftp环境
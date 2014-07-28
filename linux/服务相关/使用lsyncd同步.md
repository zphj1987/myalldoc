自动同步本地服务器（或 VPS）上的目录到另一台或多台远程服务器的办法和工具有很多，最简单的办法可能是用 rsync + cron（参考：用 VPS 给博客做镜像），这种办法有个问题就是 rsync 只能在固定时间间隔里被 cron 调用，如果时间间隔设的太短，频繁 rsync 会增加服务器负担；如果时间间隔设的太长，可能数据不能及时同步。今天介绍的 lsyncd 采用了 Linux 内核（2.6.13 及以后）里的 inotify 触发机制，这种机制可以做到只有在需要（变化）的时候才去同步。lsyncd 密切监测本地服务器上的参照目录，当发现目录下有文件或目录变更后，立刻通知远程服务器，并通过 rsync 或 rsync+ssh 方式实现文件同步。lsyncd 默认同步触发条件是每20秒或者每积累到1000次写入事件就触发一次，当然，这个触发条件可以通过配置参数调整。

lsyncd 已经在 Ubuntu 的官方源里，安装很容易：


$ sudo apt-get update
$ sudo apt-get install lsyncd

lsyncd 安装后没有自动生成所需要的配置文件和目录，需要手动创建：

	$ sudo mkdir /etc/lsyncd
	$ sudo touch /etc/lsyncd/lsyncd.conf.lua

	$ sudo mkdir /var/log/lsyncd
	$ sudo touch /var/log/lsyncd/lsyncd.{log,status}

配置 lsyncd，注意 source, host, targetdir 部分，依次是本地需要同步到远程的目录（源头），远程机器的 IP，远程目录（目标）：

	$ sudo vi /etc/lsyncd/lsyncd.conf.lua

添加如下内容：

	settings = {
	        logfile = "/var/log/lsyncd/lsyncd.log",
	        statusFile = "/var/log/lsyncd/lsyncd.status"
	}
	
	sync {
	        default.rsyncssh,
	        source = "/home/vpsee/local",
	        host = "192.168.2.5",
	        targetdir = "/remote"
			--      是否删除
			-- delete = false 
	}
配置本地机器和远程机器 root 帐号无密码 ssh 登陆，并在远程机器上（假设 IP 是 192.168.2.5）创建一个 /remote 目录：

	$ sudo su
	# ssh-keygen -t rsa
	# ssh-copy-id root@192.168.2.5
	
	# ssh 192.168.2.5
	# mkdir /remote
配置好后就可以在本地机器上启动 lsyncd 服务了，
启动服务后本地机器 /home/vpsee/local 下的目录会自动同步到远程机器的 /remote 目录下：

	$ sudo service lsyncd restart

除了同步本地目录到远程目录外，lsyncd 还可以轻松做到同步本地目录到本地另一目录，只要修改配置文件就可以了：

	$ sudo vi /etc/lsyncd/lsyncd.conf.lua
	settings {
	        logfile = "/var/log/lsyncd/lsyncd.log",
	        statusFile = "/var/log/lsyncd/lsyncd.status"，
			
			-- 提交的积累的次数
			
			-- maxDelays = 10,
			-- 同步的时间间隔
      		delay = 3
			}	
		
	
	
	sync {
		default.rsync,
		source = "/home/vpsee/local",
		target = "/localbackup"
	}
重启服务
	
	$ sudo service lsyncd restart



文件监控的数目
echo 30000000 > /proc/sys/fs/inotify/max_user_watches
###强制重启的脚本:

	#! /bin/sh
	echo 1 > /proc/sys/kernel/sysrq
	echo o > /proc/sysrq-trigger

###强制关机的脚本:

	#! /bin/sh
	echo 1 > /proc/sys/kernel/sysrq
	echo b > /proc/sysrq-trigger



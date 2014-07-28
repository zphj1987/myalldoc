##swap分区的处理

swapon -s

查看swap分区

swapoff /dev/sda5

关闭swap

创建swap分区

mkswap /dev/sda5

写入fstab

挂载

swapon  -a 

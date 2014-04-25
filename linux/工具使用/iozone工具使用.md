##Iozone工具使用
iozone是linux下的一个测试存储读写工具的一个标准工具，存储的带宽测试可以用这个进行，可以测试的数据很多，这里根据需要测试几个数据

#####下载安装包

	http://www.iozone.org/src/current/iozone3_424.tar


#####将下载的安装包拷贝到需要用的地方并解压安装包


	root@zbkc101:/usr/src# ls
	iozone3_424.tar  linux-headers-3.8.0-19  linux-headers-3.8.0-19-generic
	root@zbkc101:/usr/src# 
	root@zbkc101:/usr/src# tar -xvf iozone3_424.tar 
	root@zbkc101:/usr/src# cd iozone3_424/src/current/
	root@zbkc101:/usr/src/iozone3_424/src/current# make linux-AMD64 
	root@zbkc101:/usr/src/iozone3_424/src/current# cp iozone /sbin/
#####开始测试

iozone -i 0 -i 1 -i 2 -r 4M -s 1G -f /tmp/test

参数说明：


> **0=write/rewrite** 

> write: 测试向一个新文件写入的性能。当一个新文件被写入时，不仅仅是那些文件中的数据需要被存储，还包括那些用于定位数据存储在存储介质的具体位置的额外信息。这些额外信息被称作“元数据”。它包括目录信息，所分配的空间和一些与该文件有关但又并非该文件所含数据的其他数据。拜这些额外信息所赐，Write的性能通常会比Re-write的性能低。
> Re-write: 测试向一个已存在的文件写入的性能。当一个已存在的文件被写入时，所需工作量较少，因为此时元数据已经存在。Re-write的性能通常比Write的性能高。

> **1=read/re-read**

> Read: 测试读一个已存在的文件的性能。

> Re-Read: 测试读一个最 近读过的文件的性能。Re-Read性能会高些，因为操作系统通常会缓存最 近读过的文件数据。这个缓存可以被用于读以提高性能。

> **2=random-read/write**

> Random Read: 测试读一个文件中的随机偏移量的性能。许多因素都可能影响这种情况下的系统性能，例如：操作系统缓存的大小，磁盘数量，寻道延迟和其他。

> Random Write: 测试写一个文件中的随机偏移量的性能。同样，有许多因素可能影响这种情况下的系统性能，例如：操作系统缓存的大小，磁盘数量，寻道延迟和其他。

测试的命令如下：

	root@zbkc101:~# iozone -i 0 -i 1 -i 2 -r 4M -s 1G -f /tmp/test
 
测试输出例子：

	Iozone: Performance Test of File I/O
	        Version $Revision: 3.424 $
		Compiled for 64 bit mode.
		Build: linux-AMD64 

	Contributors:William Norcott, Don Capps, Isom Crawford, Kirby Collins
	             Al Slater, Scott Rhine, Mike Wisner, Ken Goss
	             Steve Landherr, Brad Smith, Mark Kelly, Dr. Alain CYR,
	             Randy Dunlap, Mark Montague, Dan Million, Gavin Brebner,
	             Jean-Marc Zucconi, Jeff Blomberg, Benny Halevy, Dave Boone,
	             Erik Habbinga, Kris Strecker, Walter Wong, Joshua Root,
	             Fabrice Bacchella, Zhenghua Xue, Qin Li, Darren Sawyer,
	             Vangel Bojaxhi, Ben England, Vikentsi Lapa.

	Run began: Wed Apr 23 20:32:22 2014

	Record Size 4096 kB
	File size set to 1048576 kB
	Command line used: iozone -i 0 -i 1 -i 2 -r 4M -s 1G -f /tmp/test
	Output is in kBytes/sec
	Time Resolution = 0.000001 seconds.
	Processor cache size set to 1024 kBytes.
	Processor cache line size set to 32 bytes.
	File stride size set to 17 * record size.
                                                            random  random    bkwd   record   stride                                   
              kB  reclen   write rewrite    read    reread    read   write    read  rewrite     read   fwrite frewrite   fread  freread
         1048576    4096  145374  244325   202032   163068  163827  221012                                                          

iozone test complete.

---
*power by zhangpeng* *edit time 20140424* version 1.0


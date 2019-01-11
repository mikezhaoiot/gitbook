
嵌入式项目过程中有的时候需要扩展下/tmp目录的大小，如何实现:

通过==df -h==可以查看到/tmp目录的大小，文件类型

```shell
Filesystem                Size      Used Available Use% Mounted on
tmpfs                   116.5M      1.2M    115.3M   1% /tmp
```

可以看到/tmp目录的文件类型为tmpfs,百度百科解释:

-----------------------

tmpfs临时文件系统，是一种基于内存的文件系统，它和虚拟磁盘ramdisk比较类似像，但不完全相同，和ramdisk一样，tmpfs可以使用RAM，但它也可以使用swap分区来存储，而且传统的ramdisk是个块设备，要用mkfs来格式化它，才能真正地使用它；而tmpfs是一个文件系统，并不是块设备，只是安装它，就可以使用了。tmpfs是最好的基于RAM的文件系统。[^1]


##### 系统启动的时候修改配置文件

```shell

/etc/fstab 文件

# <file system>	<mount pt>	<type>	<options>	<dump>	<pass>
/dev/root	/		ext2	rw,noauto	0	1
proc		/proc		proc	defaults	0	0
devpts		/dev/pts	devpts	defaults,gid=5,mode=620	0	0
tmpfs		/dev/shm	tmpfs	mode=0777	0	0


//修改前
tmpfs		/tmp		tmpfs	mode=1777	0	0
//修改后
tmpfs		/tmp		tmpfs	mode=1777 size=200M	0


tmpfs		/run		tmpfs	mode=0755,nosuid,nodev	0	0
sysfs		/sys		sysfs	defaults	0	0
selinuxfs   /sys/fs/selinux selinuxfs defaults 0 0
debugfs     /sys/kernel/debug debugfs defaults 0 0

```

##### 系统中临时修改

可以通过mount 重新挂载改变原来的大小

```shell
# mount -t tmpfs -o size=200M /tmp
# df -h 
Filesystem                Size      Used Available Use% Mounted on
/dev/system               2.0G    137.7M      1.8G   7% /
devtmpfs                107.1M         0    107.1M   0% /dev
tmpfs                   116.5M         0    116.5M   0% /dev/shm
tmpfs                   200.0M      4.0K    200.0M   0% /tmp
tmpfs                   116.5M     32.0K    116.5M   0% /run
/dev/data                 1.2G     68.5M      1.0G   6% /data
/dev/config               9.7M    122.0K      9.1M   1% /etc/sugr
/dev/cache               19.4M     22.0K     18.3M   0% /cache
tmpfs                   200.0M      4.0K    200.0M   0% /tmp

```

[^1]: https://baike.baidu.com/item/tmpfs

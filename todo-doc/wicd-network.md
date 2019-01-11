##### 网络管理

工作之后一直以Ubuntu系统为主力开发环境，系统自带的网络管理network和第三方开源网络wicd使用效果一般，连接，切换网络的速度比较慢，由此这里卸载系统的网络管理和第三方应用wicd，以开源库wpa_supplicant 实现一个快速连接网络，切换的小程序

##### 系统运行级别

```shell

其中rc0.d~rc.6.d文件夹下分别对应的是操作系统0-6级运行的状态下需要执行的脚本。此外还有rcS.d文件夹和rc.local文件，而在这些文件夹下的文件，都是软链文件,指向指定位置的脚本，
➜  /etc >ls rc
rc0.d/     rc1.d/     rc2.d/     rc3.d/     rc4.d/     rc5.d/     rc6.d/     rc.local*  rcS.d/  

```
发现这些软链都是指向同一个文件夹../init.d/的脚本文件，因此所有开机自启动的脚本文件都是放在/etc/init.d/目录下面的

```shell
➜  rc5.d >ls -alh 
total 20K
drwxr-xr-x   2 root root 4.0K 10月 19 14:40 .
drwxr-xr-x 191 root root  12K 10月 23 10:15 ..
lrwxrwxrwx   1 root root   30 8月  13  2017 K01vmware-USBArbitrator -> ../init.d/vmware-USBArbitrator
lrwxrwxrwx   1 root root   14 3月  21  2018 K01wicd -> ../init.d/wicd
-rw-r--r--   1 root root  677 2月   5  2016 README
lrwxrwxrwx   1 root root   16 5月   4  2017 S01apport -> ../init.d/apport
lrwxrwxrwx   1 root root   24 9月  14  2017 S01binfmt-support -> ../init.d/binfmt-support
lrwxrwxrwx   1 root root   19 4月  11  2018 S01cgmanager -> ../init.d/cgmanager
lrwxrwxrwx   1 root root   17 9月  12  2017 S01hostapd -> ../init.d/hostapd
lrwxrwxrwx   1 root root   22 5月   4  2017 S01lvm2-lvmetad -> ../init.d/lvm2-lvmetad
.....
```

这些rcN.d分别代表什么意义？其实是Linux操作系统运行级别



------------------------ 插入图片


我们可以通过runlevel查看当前系统的运行状态

```shell
➜  /etc >runlevel
N 5
```

因此我们开机自启动的脚本在rc5.d文件夹下，所有的软链指向/etc/init.d/中的脚本文件：


这些就是我们默认开机状态下，要启动的服务，如果我们将自己的脚本放在/etc/init.d/目录下，同时在rcN.d中创建软链，指向该文件（软链的名称应该是S<num><service name>,便可以开机自启这些服务。同时我们还可以修改配置文件/etc/init/rc-sysinit.conf中的DEFAULT_RUNLEVEL的值，修改操作系统默认的运行级别。[^1]

###### 系统增加网络管理

- 卸载wicd

```shell
sudo apt autoremove wicd
```

- 增加wpa_suppclait启动脚本



```shell
/etc/init.d/wpa_supplicant

➜  init.d >sudo vim /etc/init.d/wpa_supplicant
➜  init.d >cat /etc/init.d/wpa_supplicant

case "$1" in
  start)
      sudo ifconfig wlan0 up
      sudo /sbin/wpa_supplicant -Dnl80211 -iwlan0 -c/etc/wpa_supplicant.conf -B
	;;
  stop)
      killall /sbin/wpa_supplicant
	;;
  restart|force-reload)
	;;
  *)
	exit 1
	;;
esac

exit 0
```

- 建立软链接

```shell

//查看系统运行级别
runlevel
N 5

// 对应的/etc/rc5.d/目录中建立软连接
ln  -s  [源文件或目录]  [目标文件或目录]

sudo ln -s /etc/init.d/wpa_supplicant S05wpa_supplicant
```

这样完成之后系统重启之后发现网卡没有起来，/etc/init.d/wpa_supplicant 执行命令需要添加sudo
比较快速的方法在`/etc/rc.local`中添加启动的命令
```shell
# By default this script does nothing.
echo 3557 > /sys/class/backlight/intel_backlight/brightness

sudo ifconfig wlan0 up

sudo wpa_supplicant -Dnl80211 -iwlan0 -c/etc/wpa_supplicant.conf -B 

sudo udhcpc -iwlan0 -q

## vpn 
sudo sslocal -c /etc/shadowsocks/config.json -d start
exit 0 

```



另一种方法是通过　update-rc.d 添加到启动脚本[^2]

```shell
启动的脚本放入/etc/init.d/
//将脚本添加到启动脚本,50，越高表示执行的越晚
sudo update-rc.d xxx defaults 50
//移除脚本
sudo update-rc.d -f xxx.sh remove
```

##### 快速连接网络

连接网络主要调用wpa_cli和wpa_supplicant进行进程间通信，实现的网络连接，可以通过实现一个脚本调用wpa_cli快速连接网络



[^1]:[关于Ubuntu运行级别、开机启动脚本的说明](https://www.cnblogs.com/hbhzsysutengfei/p/ubuntu-runlevel-autostart-service.html)
[^2]:[Ubuntu下添加开机自动脚本](http://jackqdyulei.github.io/2016/03/06/linux-auto-script/)
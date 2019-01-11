

#### 查看分区格式
```C

# df -hT
// ext4 文件格式
Filesystem           Type            Size      Used Available Use% Mounted on
/dev/system          ext4            2.0G    112.4M      1.8G   6% /
devtmpfs             devtmpfs      107.2M         0    107.2M   0% /dev
tmpfs                tmpfs         116.5M         0    116.5M   0% /dev/shm
tmpfs                tmpfs         116.5M   1004.0K    115.5M   1% /tmp
tmpfs                tmpfs         116.5M     36.0K    116.5M   0% /run
/dev/data            ext4          613.9M    976.0K    568.2M   0% /data


[root@buildroot /]# df -hT
// ubifs 文件格式
Filesystem           Type            Size      Used Available Use% Mounted on
/dev/ubi0_0          ubifs          78.7M     67.0M     11.7M  85% /
devtmpfs             devtmpfs      106.9M         0    106.9M   0% /dev
tmpfs                tmpfs         116.5M         0    116.5M   0% /dev/shm
tmpfs                tmpfs         116.5M      2.3M    114.2M   2% /tmp
tmpfs                tmpfs         116.5M     36.0K    116.5M   0% /run
/dev/ubi1_0          ubifs          86.0M     66.2M     15.4M  81% /data
/dev/ubi1_1          ubifs          16.8M     92.0K     15.8M   1% /etc/sugr
/dev/ubi1_2          ubifs          16.8M     32.0K     15.9M   0% /cache
```


- 查看分区格式

```C
//ext4文件系统

cat /proc/inand
dev:    size   erasesize  name
inand01:    400000     80000 "bootloader"
inand02:   4000000     80000 "reserved"
inand03:  20000000     80000 "cache"
inand04:    800000     80000 "env"
inand05:   2000000     80000 "logo"
inand06:   2000000     80000 "recovery"
inand07:    800000     80000 "rsv"
inand08:    800000     80000 "tee"
inand09:   2000000     80000 "crypt"
inand10:   2000000     80000 "misc"
inand11:    400000     80000 "instaboot"
inand12:   2000000     80000 "boot"
inand13:  82000000     80000 "system"
inand14:  28000000     80000 "data"

//ubifs 文件系统

cat /proc/mtd 

dev:    size   erasesize  name
mtd0: 00200000 00020000 "bootloader"
mtd1: 00800000 00020000 "tpl"
mtd2: 00200000 00020000 "logo"
mtd3: 01000000 00020000 "recovery"
mtd4: 00f00000 00020000 "boot"
mtd5: 06480000 00020000 "system"
mtd6: 01400000 00020000 "config"
mtd7: 01400000 00020000 "cache"
mtd8: 14280000 00020000 "data"
```
- 查看整个Flash大小

```C
# cat /proc/partitions 
major minor  #blocks  name

   7        0     102400 loop0
   7        1     102400 loop1
 179        0    3702784 mmcblk0
 179        1       4096 mmcblk0p1
 179        2      65536 mmcblk0p2
 179        3     524288 mmcblk0p3
 179        4       8192 mmcblk0p4
 179        5      32768 mmcblk0p5
 179        6      32768 mmcblk0p6
 179        7       8192 mmcblk0p7
 179        8       8192 mmcblk0p8
 179        9      32768 mmcblk0p9
 179       10      32768 mmcblk0p10
 179       11       4096 mmcblk0p11
 179       12      32768 mmcblk0p12
 179       13    2129920 mmcblk0p13
 179       14     655360 mmcblk0p14
 179       96        512 mmcblk0rpmb
 179       64       2048 mmcblk0boot1
 179       32       2048 mmcblk0boot0

//计算方法
blocks 设备总块数 (1024 Bytes/block)
计算方法： blocks / 1024 = ??MB
如上: 3702784 / 1024 = 3616MB 大概3.5GB
```

```C
[root@buildroot /]# cat /proc/partitions
major minor  #blocks  name

  31        0       2048 mtdblock0
  31        1       8192 mtdblock1
  31        2       2048 mtdblock2
  31        3      16384 mtdblock3
  31        4      15360 mtdblock4
  31        5     102912 mtdblock5
  31        6      20480 mtdblock6
  31        7      20480 mtdblock7
  31        8     330240 mtdblock8
//计算方法
2048+8192+2048+16384+15360+102912+20480+20480+330240=518144
518144/1024=506MB
```

#### SWupdate 升级格式由ubifs改为ext4

```shell
//OTA开源库
amlogic/buildroot/package/swupdate

//OTA应用程序
amlogic/buildroot/board/amlogic/common/ota/ramdisk/etc/swupdate

//uboot启动参数设置
amlogic/buildroot/package/aml_ubootenv，通过库调用操作uboot环境变量

APP发送OTA升级指令-> 嵌入式设备通过调用aml_ubootenv提供的库操作uboot参数->进入recovery系统进行升级

// uboot 启动参数
bootloader/uboot-2015-dev/board/amlogic/configs/axg_s420_v1.h  -> upgrade参数

```

#### 需要修改的地方

```shell
// 添加项目代号
buildroot/build/project_config.sh

//编译的升级脚本
buildroot/build/ota_package_create.sh/

//生成固件update.zip
./buildroot/build/ota_package_create.sh 24 -e

//更改描述信息
buildroot/build/sw-description-dir/

update.zip 放入 /data下面

//设备进入OTA模式

reboot_revocery -r
```


##### OTA升级

升级后导致文件系统无法正常启动

```C
[    2.857652@1] asoc-aml-card auge_sound: dummy <-> ff642000.audiobus:pdm mapping ok
[    2.860100@1] snd_card_add_kcontrols card:ffffffc00ce11818
[    2.864592@1] aml_vrtc rtc: setting system clock to 2018-10-25 02:29:53 UTC (1540434593)
[    2.865095@1] dwc_otg: usb0: type: 2 speed: 0, config: 0, dma: 0, id: 0, phy: ffe09000, ctrl: 0
[    2.965727@1] dwc_otg: Core Release: 3.10a
[    2.965737@1] dwc_otg: Setting default values for core params
[    2.965756@1] dwc_otg: curmode: 0, host_only: 0
[    2.978117@1] dwc_otg: Using Buffer DMA mode
[    2.978121@1] dwc_otg: OTG VER PARAM: 1, OTG VER FLAG: 1
[    2.978125@1] dwc_otg: Working on port type = SLAVE
[    2.978131@1] dwc_otg: Dedicated Tx FIFOs mode
[    2.990001@1] cpucore_cooling_register, max_cpu_core_num:4
[    3.001061@1] gxbb_pm: enter meson_pm_probe!
[    3.001185@1] gxbb_pm: meson_pm_probe done
[    3.001594@1] ALSA device list:
[    3.001597@1]  d    3.656562@3] meson_uart ff803000.serial: ttyS0 use xtal(8M) 24000000 change 115200 to 115200
[    3.661617@3] Freeing unused kernel memory: 4480K
sh: : unknown operand
/dev/system is ready now.
[    3.861568@3] EXT4-fs warning (device mmcblk0p13): ext4_clear_journal_err:4699: Filesystem error recorded from previous mount: IO failure
[    3.868322@3] EXT4-fs warning (device mmcblk0p13): ext4_clear_journal_err:4700: Marking fs in need of filesystem check.
[    3.880547@3] EXT4-fs (mmcblk0p13): warning: mounting fs with errors, running e2fsck is recommended
[    3.889757@3] EXT4-fs (mmcblk0p13): recovery complete
[    3.893749@3] EXT4-fs (mmcblk0p13): mounted filesystem with ordered data mode. Opts: (null)
[    3.904749@3] EXT4-fs error (device mmcblk0p13): ext4_iget:4700: inode #49: comm init: bad extended attribute block 909719137
[    3.913422@3] Aborting journal on device mmcblk0p13-8.
[    3.918205@3] EXT4-fs (mmcblk0p13): Remounting filesystem read-only
Failed to switch root, dropping to a shell
[    3.933026@3] meson_uart ff803000.serial: ttyS0 use xtal(8M) 24000000 change 115200 to 115200
mount: mounting proc on /proc failed: Device or resource busy
-/bin/sh: source: line 57: can't open '/etc/property_set.sh': No such file or directory
# [    9.154420@0] random: fast init done
[   77.615241@3] random: crng init don
```
- [Linux下查询磁盘空间和扩充磁盘空间](http://ningg.top/linux-disk-space/)




## ssid 
ssid : i-amlogic
psk : aml4yourlife

ssid : 431-zhiwei_5G
psk : 123123123


## 去掉登录密码

- ssh需要修改/etc/ssh/sshd_config
- #PermitRootLogin prohibit-password   ->改为  PermitRootLogin yes
- #PermitEmptyPasswords no             ->改为  PermitEmptyPasswords yes
改完运行：
- /etc/init.d/S50sshd  stop
- /etc/init.d/S50sshd  start



## V1400项目

### 灯效

- 唤醒: 1圈青色灯亮起
- 正在聆听: 1圈青色灯亮起
- 结束聆听: 2颗青色灯 2颗蓝色灯交替闪烁
- 语音加载: 2颗青色灯 2颗蓝色灯交替闪烁
- 正在回答: 1圈蓝色灯 1圈青色灯交替缓慢显示 




## SB9220项目
S02llightDaemon

- start-stop-daemon -S -m -p /var/run/lightDaemon.pid  -b -x /sbin/lightDaemon 

- 启动和停止系统守护程序 


## buildroot

- 默认配置文件: mesonaxg_ha01_32_debug_defconfig
- Amlogic配置文件: mesonaxg_s420_32_release_defconfig

### kernel 
- 默认配置文件: arch/arm64/configs/meson64_smarthome_defconfig


### GPIO 
GPIOZ_0 -> MIC_LED -> C6

引脚编号: include/dt-bindings/gpio/mesonaxg-gpio.h

GPIOZ_0 = 14+1 = 15 : 直接操作15: echo 15 > export




## SN3218 

SDB口需要高电平 

slaveaddr : 0x54 

LED_SDB(65) -> C2- > GPIOZ_7 -> 14+7+1 = 22

Saleae16 16路 逻辑分析仪 

### 端口

- 单灯: LED401 : 电源灯　
- SN3218- 2 / 3 / 4 / 5 / 6 /  单灯
- SN3218  8 / 9 / 10 -> 对应一颗RGB三色灯
- 描述：
　- LED控制器2(OUT7 ~ OUT12)
  - 0x02 -> Blue 
  - 0x04 -> Red
  - 0x08 -> Green 
- 先控制SDB echo 1 > /sys/class/leds/trumpet-led/brightness
- 应用层区分单板
  - cat /proc/device-tree/amlogic-dt-id 
  - # ha01 -- va1400
  - # ha02 -- sb9220



## 驱动中添加灯效

- git clone ssh://mike@gerrit.sugr.com:29418/intel/sofia3gr/linux-3.10
- kernel/aml-4.9/arch/arm64/boot/dts/amlogic/axg_s420_v03.dts 
- dos2unix 格式转换

```

ERROR: DOS line endings
#6473: FILE: drivers/misc/misc_sn3236.c:654:
+static int sn3236_i2c_remove(struct i2c_client *client) {^M$

ERROR: open brace '{' following function declarations go on the next line
#6473: FILE: drivers/misc/misc_sn3236.c:654:
+static int sn3236_i2c_remove(struct i2c_client *client) {

ERROR: DOS line endings
#6474: FILE: drivers/misc/misc_sn3236.c:655:
+^Isn3236_enable(client, false);^M$


```


### 驱动添加sn3218驱动

- linux sysfs_create_group()创建sysfs接口   http://blog.csdn.net/chenmiaowu88/article/details/54427013
- 内核最终配置: amlogic/output/mesonaxg_ha02_32_release/build/linux-amlogic-4.9-dev/.config 

### dts led 

- driver/leds/leds-gpio.c

- 参考 aml-4.9/sound/soc/codecs/amlogic/tas5707.c 

- 关闭驱动调用脚本  /usr/bin/snleds-test -c 

- recovery 关键字 recovery_part


## 编译　
- source buildroot/build/setenv.sh -> Which would you like? [5] 13 (mesonaxg_ha01_32_debug)

#### 编译内核
- make linux-rebuild
- 服务器编译　make -j16 


### 烧录固件
update partition boot boot.img
update bulkcmd "reset"


## 同步

- repo sync 
- repo start sense  --all 


## git 配置

- git config --global user.email zhaomm@sugrsugr.com 
- git config --global user.name zhaomm
- git config --global core.editor vim  
- ssh-keygen -t rsa -C "zhaomm@sugrsugr.com"   生成密钥
- gerrit 添加密钥　

- 退回仓库
  - git reset HEAD~2
  - git fetch sugr sense 
  - git rebase sugr/sense 



rm -rf output/mesonaxg_ha02_32_release/build/avs-sugr

  



### 单独验证avs 

- buildroot/package/avs-sugr/avs-sugr.mk 中的 AVS_SUGR_TARGET_DIR = $(AVS_SUGR_OUT_DIR)/_install   @111 
- make avs-sugr-rebuild 
- 拷贝 output/mesonaxg_ha02_32_release/build/AVS_output_sugr/_install/ 到开发板root目录
- 解压tar -xvf  avs-test.tar   -C /
- 启动avs: /etc/init.d/S91avs-login start 或 手动运行 /usr/bin/SampleApp /etc/AlexaClientSDKConfig.json /etc NONE
- 查看打印tail -f /tmp/server.log 或 /tmp/avs.log 

- 修改音量: 手动运行 /usr/bin/SampleApp /etc/AlexaClientSDKConfig.json /etc NONE, 通过u / d 操作音量


### 设备树

- cat i2c@ff805000/sn3218_54@54/sn3218,led-order | od -x   


- ~/amlgoic/buildroot/board/amlogic/common/ota/ota-axg


### ntp说明

ntpdate -u cn.pool.ntp.org 

buildroot/package/ntp/S49ntp  相关代码

busybox 中的 /usr/sbin/ntpd -q -p cn.pool.ntp.org 

avs启动　S91avs-login

#### 服务器

cn.pool.ntp.org 
0.pool.ntp.org 
pool.nto.org

aplay -Dhw:0,2 /tmp/*.mp3

0: 声卡　
2: 第几个设备




set_source.sh

#### 账号
-----------------------------

| 网址 | 账号 | 密码 |用途|
|--------|--------|--------|------------|
| http://gerrit.sugr.com/       |    mike    |    123    |gerrit审核网页，201服务器|
| http://192.168.1.201:8089       |   rose     |    Rose123    |jenkins网页自动编译，只有rose账号有权限|
| http://192.168.1.201:8089       |   mike     |    `    |jenkins网页自动编译，只有rose账号有权限|
| 192.168.1.201       |   jenkins    |   SugrSugr   |jenkins服务器|
| rddoc.sugrsugr.com       |   Sugrman   |   ShareBeautifulSound   |云端服务器存放文档|
|https://op-manager.sugrsugr.com |   zhaomm@sugrsugr.com   |   XQX13579  | 在线OTA升级|
|  http://buglist.sugrsugr.com       |   mikezhao   |   Sugr.123  | 禅道bug管理系统|


#### 设备登录密码

```shell
//SenseA 平台(A113X)

登录密码:[Sugrman]

```

#### 下载源代码


修改本机hosts,linux本机目录/etc/hosts,中添加

192.168.1.201  gerrit.sugr.com

初始化版本repo仓库

repo init -u ssh://USERNAME@192.168.1.201:29418/amlogic/sense/manifests.git  -b default

同步仓库代码

repo sync

新建本地sense分支（必须）

repo start sense --all

新建gerrit审核分支（必须）

repo forall -c 'git config remote.sugr.push refs/heads/*:refs/for/*'

针对所有git仓库远程提交

repo upload        

#### 编译
-----------------------------

Buildroot第一次编译过程中会自动从网络下载所需的第三方依赖包源码，源码比较大(1.3GB)，下载比较耗时，
可以直接Copy我已经下载好的包，替换自己编译工程的"buildroot/dl”目录。
第三方依赖包源码放在201服务器，请自行下载，参考命令如下：
scp -r share@192.168.1.201:/home/share/amlogic/buildroot/dl  buildroot/dl
201服务器share账号的密码为share


###### 去掉登录密码
----------------------------
```shell
- ssh需要修改/etc/ssh/sshd_config
- #PermitRootLogin prohibit-password   ->改为  PermitRootLogin yes
- #PermitEmptyPasswords no             ->改为  PermitEmptyPasswords yes
改完运行：
- /etc/init.d/S50sshd  stop
- /etc/init.d/S50sshd  start
```


### buildroot 开启connman 

配置项只需要关注Defined, connman 只有依赖项全部符合之后,才会有connman选项  
- Toolchain -> Enable WCHAR support 
- Target packages -> Networking applications -> connman 

- 更改buildroot 配置
   - 加载默认配置：　make mesonaxg_sa01_32_debug_defconfig 
   - 配置选项: make menuconfig <>
   - 对比不同: 把buildroot/.config 中新添加的选项　复制到　mesonaxg_sa01_32_debug_defconfig　
   - 编译:  buildroot/目录下: make  

#### wifi驱动 multi_wifi_load_driver
　- 源码路径: /home/zhaomm/project/amlogic/buildroot/package/aml_util/src 


问题点:

 Error /net/connman/technology/wifi: Not supported
  https://wiki.archlinux.org/index.php/ConnMan#Error_.2Fnet.2Fconnman.2Ftechnology.2Fwifi:_Not_supported
  表示　wpa_supplicant -u 没有启动, dbus　没有启动

Error /net/connman/technology/wifi: No carrier
　connmanctl enable wifi 没有开启

#### GPIO
GPIOZ_0 -> MIC_LED -> C6

引脚编号: include/dt-bindings/gpio/mesonaxg-gpio.h

GPIOZ_0 = 14+1 = 15 : 直接操作15: echo 15 > export



#### 驱动中添加灯效

- git clone ssh://mike@gerrit.sugr.com:29418/intel/sofia3gr/linux-3.10
- kernel/aml-4.9/arch/arm64/boot/dts/amlogic/axg_s420_v03.dts 
- dos2unix 格式转换

```shell
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

  



#### 单独调试AVS

```C

//修改配置文件

 buildroot/package/avs-sugr/avs-sugr.mk 中的 AVS_SUGR_TARGET_DIR = $(AVS_SUGR_OUT_DIR)/_install   @111

// 打开AVS全部打印
-DCMAKE_BUILD_TYPE=DEBUG

//编译
make avs-sugr-rebuild

//拷贝
由于加了打印的log，编译生成的动态库会恨到，需要通过arm-linux-gnueabihf-strip缩小下库的大小
output/mesonaxg_ha02_32_release/build/AVS_output_sugr/_install/ 到开发板root目录

//解压
tar -xvf  avs-test.tar   -C /

//启动avs:
/etc/init.d/S91avs-login start 或 手动运行 /usr/bin/SampleApp /etc/AlexaClientSDKConfig.json /etc NONE

//查看打印
查看打印tail -f /tmp/server.log 或 /tmp/avs.log

//修改音量:手动运行
/usr/bin/SampleApp /etc/AlexaClientSDKConfig.json /etc NONE, 通过u / d 操作音量

//查看详细打印

/usr/bin/SampleApp /etc/AlexaClientSDKConfig.json /etc DEBUG9

// gdb 调试
gdb /usr/bin/SugrVoice
//进入gdb 交互终端
r -C /etc/SugrVoiceConfig.json -K /etc -L DEBUG9

```

#### bluez调试


TODO 

hciconfig -a

./package/bluez5_utils/bluez_tool.sh  脚本没有区分版本

### 设备树

- cat i2c@ff805000/sn3218_54@54/sn3218,led-order | od -x

- ~/amlgoic/buildroot/board/amlogic/common/ota/ota-axg






## 编译avs-sugr 

一般编译之后, 重新编译需要删除　rm -rf output/mesonaxg_sa01_32_debug/build/avs-sugr/.stamp_configured 　

然后编译
打开cmake 的详细编译: package/avs-sugr/avs-sugr.mk
取消注释: #-DCMAKE_VERBOSE_MAKEFILE:BOOL=ON \

之后编译的过程可以看log 确认添加的内容是否编译

查看依赖库:  objdump -x output/mesonaxg_sa01_32_debug/target/usr/bin/SugrVoice | grep NEEDED


##### 测试录音小工具
- gerrit 仓库:  git clone ssh://mike@gerrit.sugr.com:29418/sugrapps/snsr-tools
- 编译: make armhf=1 debug , 输出文件: bin/sugr-spot
- 测试唤醒词: sugr-spot /usr/share/avs/thfft_alexa_a_enus_v3_1mb.snsr 16
- 16 代表0x10, 代表fae_dump_org.pcm, 格式: 16khz, 8channels 16bit

fae_dump_agc.pcm
fae_dump_org.pcm
fae_dump_out.pcm
fae_dump_rec.pcm
fae_dump_ref.pcm


#### jenkins自动编译

jenkins_build.sh　-> jenkins_firmware_create.sh -> ota_package_create.sh

//推送到板子上面
jenkins_do_update.sh


#### AVS 添加　


- $(STAGING_DIR)=/home/zhaomm/Work/a113x-code/output/mesonaxg_lf01_32_release/host/usr/arm-linux-gnueabihf/sysroot/usr/include




 while true; do sleep; adb shell; done 


#### iot 字段

//开灯
{	
  "op": "light.power",
 	"endpoint": "LF01VRQAXXWN",
 	"messageId": 123,
	"version": "v1",
 	"attachInfo":{
 	    "value":1
 	}
}


{	
  "op": "light.atmosphere",
 	"endpoint": "LF01VRQAXXWN",
 	"messageId": 123,
	"version": "v1",
 	"attachInfo":{
 	    "id":1,
      "status":1
 	}
}


{
  "messageId" : "1545222484310",
  "op" : "light.power",
  "endpoint" : "LF01VRQAXXWN",
  "resourceId" : "519BEE37-22E6-4AE9-8CD3-D76E752427E2",
  "attachInfo" : {
    "value" : 1
  },
  "version" : "v1"
}

{
  "messageId" : "1545222484310",
  "op" : "light.atmosphere",
  "endpoint" : "LF01VRQAXXWN",
  "resourceId" : "519BEE37-22E6-4AE9-8CD3-D76E752427E2",
  "attachInfo" : {
	    "id":25,
        "status":1
  },
  "version" : "v1"
}


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

#### Git 配置

```shell
  git config --global user.email zhaomm@sugrsugr.com
  git config --global user.name zhaomm
  git config --global core.editor vim
  ssh-keygen -t rsa -C "zhaomm@sugrsugr.com"   生成密钥
  gerrit 添加密钥

退回仓库
  git reset HEAD~2
  git fetch sugr sense
  git rebase sugr/sense
```

#### 源码下载编译

```shell

修改本机hosts,linux本机目录/etc/hosts,中添加

192.168.1.201  gerrit.sugr.com

初始化版本repo仓库

repo init -u ssh://USERNAME@192.168.1.201:29418/amlogic/sense/manifests.git  -b default

repo init -u ssh://mike@192.168.1.201:29418/amlogic/sense/manifests.git  -b default

同步仓库代码

repo sync

新建本地sense分支（必须）

repo start sense --all

新建gerrit审核分支（必须）

repo forall -c 'git config remote.sugr.push refs/heads/*:refs/for/*'

针对所有git仓库远程提交

repo upload

第三方依赖包源码放在201服务器，请自行下载，密码:share 参考命令如下：

scp -r share@192.168.1.201:/home/share/amlogic/buildroot/dl/ buildroot/dl

```

- 下载docker环境

// 查看正在运行docker 
dpcker ps 

//终止docker 容器

docker kill CONTAINER

// 运行 

docker run -itd --restart=always -p 10022:22 -v YOUR_ABSOLUTELY_WORK_PATH:/home/sugr/work/ --name rdenv rdenvimg:v0.1 /root/Start.sh
改为
docker run -itd --restart=always -p 10022:22 -v /home/mike/Work/a113x-code:/home/sugr/work/ --name a113x a113x:v0.2 /root/Start.sh

//ssh 登录docker 容器, 密码12345678

ssh sugr@127.0.0.1 -p 10022  


- 分支提交

[将Git工程提交到两个不同的仓库](https://blog.csdn.net/xdyuchen/article/details/51392357)

由于现在最新代码存放在Gerrit上，一般不会新建开发分支。本地的开发分支会放在GitLab，临时采用修改.git/config文件提交到两个不同的仓库


```
## buildroot 

[remote "sugr"]
    url = ssh://mike@192.168.1.201:29418/amlogic/sense/buildroot
    review = review.source.android.com
    projectname = buildroot
    fetch = +refs/heads/*:refs/remotes/sugr/*
    push = refs/heads/*:refs/for/*
[remote "origin"]
    url = ssh://git@gitlab.sugrsugr.com:9022/A1/buildroot
    review = ssh://git@gitlab.sugrsugr.com:9022/A1/
    projectname = buildroot
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "sense"]
    remote = sugr
    merge = sense
[branch "loft-dev"] 
    remote = origin
    merge = refs/heads/loft-dev

## avs-sugr





```

###### 编译
-----------------------------

Buildroot第一次编译过程中会自动从网络下载所需的第三方依赖包源码，源码比较大(1.3GB)，下载比较耗时，

可以直接Copy我已经下载好的包，替换自己编译工程的"buildroot/dl”目录。

第三方依赖包源码放在201服务器，请自行下载，参考命令如下：

```shell
scp -r share@192.168.1.201:/home/share/amlogic/buildroot/dl  buildroot/dl
201服务器share账号的密码为share
```

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


#### 开启connman

- 配置项只需要关注Defined, connman 只有依赖项全部符合之后,才会有connman选项

```shell
- Toolchain -> Enable WCHAR support
- Target packages -> Networking applications -> connman

- 更改buildroot 配置
   - 加载默认配置：　make mesonaxg_sa01_32_debug_defconfig
   - 配置选项: make menuconfig <>
   - 对比不同: 把buildroot/.config 中新添加的选项　复制到　mesonaxg_sa01_32_debug_defconfig
   - 编译:  buildroot/目录下: make

- wifi驱动multi_wifi_load_driver
　- 源码路径: /a113x-code/buildroot/package/aml_util/src

```

- 问题点:

```shell
 Error /net/connman/technology/wifi: Not supported
  https://wiki.archlinux.org/index.php/ConnMan#Error_.2Fnet.2Fconnman.2Ftechnology.2Fwifi:_Not_supported
  表示　wpa_supplicant -u 没有启动, dbus　没有启动

Error /net/connman/technology/wifi: No carrier
　connmanctl enable wifi 没有开启

```

- GPIO应用层操作

```shell

GPIOZ_0 -> MIC_LED -> C6

引脚编号: include/dt-bindings/gpio/mesonaxg-gpio.h

GPIOZ_0 = 14+1 = 15 : 直接操作15: echo 15 > export

```

#### LED

- 驱动中添加灯效

```shell
  git clone ssh://mike@gerrit.sugr.com:29418/intel/sofia3gr/linux-3.10
  kernel/aml-4.9/arch/arm64/boot/dts/amlogic/axg_s420_v03.dts 
  dos2unix 格式转换
```

- 报错提示

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

- 驱动添加SN3218

```shell
 linux sysfs_create_group()创建sysfs接口

 参考http://blog.csdn.net/chenmiaowu88/article/details/54427013

 内核最终配置: amlogic/output/mesonaxg_ha02_32_release/build/linux-amlogic-4.9-dev/.config 
```

- LED DTS

```shell
 cat i2c@ff805000/sn3218_54@54/sn3218,led-order | od -x

 ~/amlgoic/buildroot/board/amlogic/common/ota/ota-axg
 
```


#### 调试AVS

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

---------------------------------------
调试这个问题需要打开全部打印分析下：
//进入设备
adb shell 

//关闭S91avs-login进程
/etc/init.d/S91avs-login stop

//修改文件
vi /etc/init.d/S91avs-login

/usr/bin/SugrVoice -C $sugr_json -C $comms_json -K /etc -SB backend -L NONE  >>/tmp/avs.log  2>&1 &
改为
/usr/bin/SugrVoice -C $sugr_json -C $comms_json -K /etc -SB backend -L DEBUG9 >>/tmp/avs.log  2>&1 &

//同步
sync

// 重启进程或重启设备
/etc/init.d/S91avs-login restart

查看依赖库:  objdump -x output/mesonaxg_sa01_32_debug/target/usr/bin/SugrVoice | grep NEEDED

```

#### bluez调试

```shell

hciconfig -a

./package/bluez5_utils/bluez_tool.sh  脚本没有区分版本

```



#### jenkins自动编译

```shell

jenkins_build.sh　-> jenkins_firmware_create.sh -> ota_package_create.sh

//推送到板子上面
jenkins_do_update.sh

```
#### 调试IOT

```shell

Sugr/Sense/Loft/Light/Request/LF015RQDLXW7

//开灯
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

//氛围灯控制
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


```

#### 数据库sqlite操作

```shell
//i打卡数据库
sqlite3 sugrvoice.db
//获取数据库中数据
select * from sugrvoice;

```

#### 调试技巧

- 循环读取adb: while true; do sleep; adb shell; done



#### 参考

-  [Linux ALSA声卡驱动之四：Control设备的创建](https://blog.csdn.net/DroidPhone/article/details/6409983)
-  [amixer和alsamixer使用说明](https://blog.csdn.net/meirixuexi/article/details/71513007)


 arecord -Dhw:0,3 -d 5 -c 8 -r 16000 -f S16_LE /tmp/test.wav & gst-play-1.0 /usr/share/sense/testaudios/connected-fail.mp3 
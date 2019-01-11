#### 登录密码

[Sugrman]

scp -P 14401 

### 添加单板

- **配置文件继承JY01** 

```C
cp mesonaxg_jy01_32_release_defconfig mesonaxg_loft_32_release_defconfig
```
- 裁剪
  - BM64 
  - SN3236_LED

- **增加编译选项**

```C
buildroot/build/setenv.sh
```

### 内核

#### buildroot 编译选项
- Defconfig name
- Device Tree Source file names
 - dts中的amlogic-dt-id = "axg_loft"
 - BR2_LINUX_KERNEL_INTREE_DTS_NAME="axg_loft"

#### 音频驱动

- 系统配置文件

```C
kernel/aml-4.9/arch/arm64/configs/meson64_smarthome_defconfig
```
~~~~
- 配置DTS文件

```C
cp kernel/aml-4.9/arch/arm64/boot/dts/amlogic/axg_jy01.dts axg_loft.dts
```

- 驱动文件

```C
Codec : kernel/aml-4.9/sound/soc/codecs/amlogic/tas575x.c tas575x.h 
Platform : kernel/aml-4.9/sound/soc/amlogic
```

- 配置IIC从机地址
 - ADR0 = 0 ADR1 = 1 -> 0X9D/0X9C
 - 从机地址: 0x4e, 计算从机地址不包含读写位, 最高位为0, -> 0100 1110 


aplay -Dhw:0,2 /usr/share/sense/testaudios/mic_on.mp3


aplay: main:788: audio open error: Device or resource busy 

#### 红外驱动

```C 
drivers/amlogic/input/remote/remote_meson.c　
```
#### 灯效 

```C
arch/arm64/boot/dts/amlogic/axg_loft.dts 禁止sn3236: sn3236_3c@3c 
```

### eMMC

#### 原厂补丁

Amlogic 提供了是三个补丁
```C
0001-add-emmc-support-bootloader-part-3-3(1).patch
0001-add-emmc-support-buildroot-part-1-3(2).patch
0001-add-emmc-support-kernel-part-2-3(1).patch
```

#### kernel 补丁出现冲突

参考[Git Patch使用: 生成,导入,解决冲突](https://blog.csdn.net/matrix_laboratory/article/details/45870639)解决 

- git am patch
- git apply PATCH --reject ,生成.rej文件, 参考该文件解决冲突
- git am --continue 


### 蓝牙模块

- 蓝牙广播设备名称: TTC_infrared 

deb https://download.docker.com/linux/ubuntu zesty edge


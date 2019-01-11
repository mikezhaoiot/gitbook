### 调试常用

Y~Wy7^$n7x%,$=NM

769979801 

jason@sugrsugr.com Sugr140333
sugr@sugrsugr.com Sugr140331
ericling@sugrsugr.com Sugr140331 支持Box　

修改sugr/build.sh 下的　build_install_applicantion @136 中的 
install_dir = $BOARD_ROOTFS/usr/fs/sugr/app 改为　install_dir=/mnt/nfs/app

mount -t nfs -o nolock 192.168.3.134:/mnt/nfs /mnt/nfs

'echo "set ' .. mode .. ' ' .. cint ..' '.. pint .. ' ' .. loop .. '" > /dev/sugr_misc'

'echo "set '6' '0' '0' '1'" > /dev/sugr_misc'

./push_firmware_cdn.sh -fw ../../../out/S105/images/update_D_20180615-2100_HWV_S105_AV_.zip -v 20180615-2100 --code S105

### SenseA平台编译 

- mesonaxg_ha01_release_release

- 查看编译之前的依赖

- make show-targets 

显示出本次配置所要编译所有的目标

- make target_rebuild 

- make avs_sugr-rebuild 


#### BM64调试


1. 首先查看/tmp/avs.log 是否有"BM64 ACK OK!!!"


初始化步骤

/usr/bin/gpio_bm64.sh 1 

start_init_bm64((char *)"/dev/ttyS3",115200,&event_callback)
 



　

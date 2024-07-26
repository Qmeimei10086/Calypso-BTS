# 本程序仅供学习，请勿用于非法
# 指路:
OpenBTS编译与使用  
https://github.com/Qmeimei10086/OpenBTS-gsm-mitm  
# 前言
本来仓库自osmocom-bb的calypso-BTS项目，该项目致力于在c118上实现transceiver功能，从而使c118成为廉价的BTS设备  
该仓库仅作为记录和保存文件方便使用  
## 部分过程参考
https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw%3D%3D&mid=2458280897&idx=1&sn=694e08910e1e32c1159a8eab34a374b5&chksm=b181534b86f6da5d01eda9cd5ac2c10c4dbd93b3c9197f0c3aaddf06b9e9ea297556f625c66a&scene=27&_refluxos=a10
# 编译
请确保你已经成功安装完编译osmocombb的环境，参考：https://github.com/Qmeimei10086/mobile-gsm-mitm  
## 编译本体
```javascript
unzip osmocombb-sylvain
cd osmocom-bb/src
make firmware HOST_layer23_CONFARGS=--enable-transceiver
make osmocon HOST_layer23_CONFARGS=--enable-transceiver
make layer23 HOST_layer23_CONFARGS=--enable-transceiver
```
ps：  
1.其实如果遇到编译firmware时出现bug，请在make后加上 -i 参数  
2.其实可以直接 make HOST_layer23_CONFARGS=--enable-transceiver，但没必要  
3.如果你是自己下载的osmocombb，把 osmocom-bb/src/target/firmware 下的 Makefile 中的DCONFIG_TX_ENABLE 宏打
开： 
#Uncomment this line if you want to enable Tx (Transmit) Support.
#CFLAGS += -DCONFIG_TX_ENABLE
然后到 src 目录下编译，不过我已经帮你打开了捏(/ω＼)  
## 关于release版
和之前一样，我发布了编译好的文件以及所需动态链接库，解压完以后
```javascript
cd trx
cp *.so.* /usr/lib
chmod +x ./*
```
# 配置
如果以上步骤没有问题，你就可以开始修改OpenBTS.db了，以下需要修改
```javascript
GSM.Radio.NeedBSIC = 1
GSM.Radio.Band = 900
GSM.CellSelection.Neighbors =(留空)
GSM.RACH.MaxRetrans = 3
GSM.RACH.TxInteger = 8
GSM.Radio.C0 = 你的afrcn（注意不要和别的基站重叠了）
Control.LUR.OpenRegistration =.*
```
在OpenBTS目录下创建transceiver文件，并且用chmod +x transceiver为他赋予权限，内容为 
```javascript
#!/bin/bash
exec <你osmocombb的路径>/src/host/layer23/src/transceiver <你附近最强基站的afrcn>
```
# 使用
```javascript
#刷入固件
cd osmocom-bb/src/host/osmocon
./osmocon -m c123xor -p /dev/ttyUSB0 ../../target/firmware/board/compal_e88/trx.comapalram.bin -s /tmp/trx

启动openbts
./sipauthserve
./OpenBTS
```
不出意外你的openbts已经成功运行起来了啦  
ps：  
我其实将transceiver的unix_socket路径修改为/tmp/trx，这是防止在gsm中间人攻击时和
mobile程序的unix_socket路径重叠 
# 关于作者
bilibili：https://space.bilibili.com/431312664?spm_id_from=333.1007.0.0  
有问题来这里找我，本人已高三，可能不能及时回  




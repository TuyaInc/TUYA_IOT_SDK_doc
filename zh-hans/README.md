# :smile:

## SDK适用范围

- 设备需支持 linux 或 Android 系统
- 支持TCP/IP协议栈

## 编译说明

- linux平台：涂鸦根据客户提供的工具链(toolchain)编译成的静态库，更新到github上。
- Android平台：
    我们是用bionic库的；
    客户提高安卓平台系统位数 和 Android API版本号
    涂鸦根据客户提供的 API 版本号，使用NDK编译出工具链arm-linux-androideabi-gcc，并编译成的相关的库，更新到github上。

## linux平台下涂鸦sdk种类

- iotSDK，目前主要基于wifi单品设备的开发，如扫地机、智能音响等品类；

  仓库地址：https://github.com/TuyaInc/TUYA_IOT_SDK
- 入云网关sdk, 提供网关连接到涂鸦云的功能，和子设备数据交互需要自行开发，可开发zigbee网关、mesh网关、433网关等；

  仓库地址：https://github.com/TuyaInc/TUYA_ROUTER_GW_SDK
- zigbee网关sdk, 封装了涂鸦成品网关TYGWZ-01的所有功能，网关需集成涂鸦提供的TYZS4型号zigbee模组，和子设备数据交互无需开发，可快速完成zigbee网关的开发；

  还集成了一键配网功能，对于具有路由器功能的网关产品，可实现通过路由器直接广播配网信息包，把处于Smart配网模式的涂鸦wifi设备添加到手机APP上；

  仓库地址：https://github.com/TuyaInc/TUYA_ROUTER_GW_SDK

## 交叉编译器说明
- 因系统平台差异性较大，涂鸦不提供交叉编译器；
- 先去sdk仓库地址查看目前已经打包的是否有适配你设备系统的SDK; 如果没有，请按如下目录格式打包，通过开发者工单系统提交给涂鸦输出SDK，提交链接：https://iot.tuya.com/council/ticket/create

具体格式参考：

```c
$ tree -L 2 arm-linux-gcc-4.5.2/
arm-linux-gcc-4.5.2/
├── README.md
└── toolchain      
    ├── arm-linux.tar.gz
    └── build_path

2 directories, 3 files
$ cat arm-linux-gcc-4.5.2/toolchain/build_path 
export TUYA_SDK_TOOLCHAIN_ZIP=arm-linux.tar.gz              #压缩后的交叉编译器文件名称
export TUYA_SDK_BUILD_PATH=arm-linux/bin/arm-linux-         #交叉编译器的相对路径
export TUYA_SDK_INCLUDE_PATH=arm-linux-gnueabihf/include    #交叉编译器的头文件路径
```




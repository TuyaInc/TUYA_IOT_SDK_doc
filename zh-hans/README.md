## SDK适用范围

- 设备需支持 linux 或 Android 系统
- 支持TCP/IP协议栈

## 编译说明

- linux平台：涂鸦根据客户提供的工具链(toolchain)编译成的静态库，更新到github上。
- Android平台：涂鸦根据客户提供的 API 版本号，使用NDK产生工具链，并编译成的相关的库，更新到github上。


## linux平台下涂鸦sdk种类

- iotSDK，目前主要基于wifi单品设备的开发，如扫地机、智能音响等品类；

  仓库地址：https://github.com/TuyaInc/TUYA_IOT_SDK
- 入云网关sdk, 提供网关连接到涂鸦云的功能，和子设备数据交互需要自行开发，可开发zigbee网关、mesh网关、433网关等；

  仓库地址：https://github.com/TuyaInc/TUYA_ROUTER_GW_SDK
- zigbee网关sdk, 封装了涂鸦成品网关TYGWZ-01的所有功能，网关需集成涂鸦提供的TYZS4型号zigbee模组，和子设备数据交互无需开发，可快速完成zigbee网关的开发；

  还集成了一键配网功能，对于具有路由器功能的网关产品，可实现通过路由器直接广播配网信息包，把处于Smart配网模式的涂鸦wifi设备添加到手机APP上；

  仓库地址：https://github.com/TuyaInc/TUYA_ROUTER_GW_SDK




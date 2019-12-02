# 场景与联动
- 场景与联动的区别与联系，联动的的触发条件改为点击执行，即为场景。

## 智能-自动化

## 本地场景联动
- 本地联动条件
    - 创建自动化条件时必须为同一网关下子设备的动作，不能为温度、湿度、天气等来着云端的数据
    - 创建自动化任务时必须为同一网关下子设备的动作、或者此网关下的其他本地联动场景
- 设备联动逻辑会存储在网关本地上;
- 手机app端触发场景.(手机APP和网关在同一局域网下)
- 子设备端触发场景。(网关可以断网)
- 此功能联网SDK基线已经支持，无需开发；场景联动创建时，网关应用层上无感知的，只有场景触发时，会通过dp回调通知应用层，和普通指令下发处理一样。

### 场景联动创建
```uml
@startuml
title 创建场景
participant TuyaApp
participant tuya_sdk
participant gateway
participant TuyaCloud

Note over TuyaApp:用户创建场景
TuyaApp->TuyaCloud:场景联动逻辑
TuyaCloud-->tuya_sdk:"protocol":49 ruleId
tuya_sdk->TuyaCloud:获取场景逻辑 tuya.device.linkage.rule.query
tuya_sdk-->tuya_sdk:判断是否满足本地化条件,满足把逻辑存入本地
tuya_sdk->TuyaCloud:场景联动本地化 tuya.device.linkage.rule.localize
TuyaCloud-->tuya_sdk:OK
@enduml
```

### 场景联动本地执行
- APP和网关处于同一局域网下，从APP上点击执行，走局域网下发
- 执行条件为网关下子设备时，可从子设备端触发；比如执行条件为zigbee门磁开，当门磁上报开状态到网关时，网关会查询此子设备是否有联动逻辑，有则调用对应设备的dp回调通知网关应用层执行子设备联动；

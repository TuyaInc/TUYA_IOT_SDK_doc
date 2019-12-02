## 子设备在线状态管理

子设备和网关端的心跳保活可由用户控制；tuya_sdk默认子设备是永久在线的，没有启动心跳保活；

### tuya_iot_dev_online_stat_update

说明：如果网关应用层本身有管理子设备上线和离线的功能，子设备离线或上线时，主动调用此接口更新云端的子设备在线状态即可；
```c
/***********************************************************
 * @Function:tuya_iot_dev_online_stat_update
 * @Desc:   更新子设备在线状态
 * @Param:  dev_id，子设备id
 * @Param:  online, 是否在线
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_dev_online_stat_update(IN CONST CHAR_T *dev_id,IN CONST BOOL_T online);
```

### 开启tuya_sdk的心跳检测机制

```uml
@startuml
title sub device heartcheck
participant TuyaApp
participant sub_device
participant gateway
participant tuya_sdk
participant TuyaCloud

gateway->tuya_sdk:tuya_iot_sys_mag_hb_init(DEV_HEARTBEAT_SEND_CB)\n开启tuya_sdk心跳检测
tuya_sdk-->gateway:return OPRT_OK
gateway->tuya_sdk:tuya_iot_set_dev_hb_timeout\n设置心跳超时时间
tuya_sdk-->gateway:return OPRT_OK
sub_device->gateway:上报数据
gateway->tuya_sdk:tuya_iot_fresh_dev_hb 刷新子设备心跳时间
tuya_sdk-->gateway:return OPRT_OK
loop 根据超时时间，周期性刷新子设备心跳
tuya_sdk->gateway:DEV_HEARTBEAT_SEND_CB 提醒刷新心跳
gateway->sub_device:发送心跳
sub_device-->gateway:应答
gateway->tuya_sdk:tuya_iot_fresh_dev_hb 刷新子设备心跳时间
tuya_sdk-->gateway:return OPRT_OK
end
Note over tuya_sdk: 如果心跳超时
tuya_sdk->TuyaCloud:子设备离线
TuyaCloud-->TuyaApp:通知子设备离线
@enduml
```

#### tuya_iot_sys_mag_hb_init

说明： 开启tuya_sdk的心跳检测机制，需要首先调用此接口初始化

```c
/***********************************************************
 * @Function:tuya_iot_sys_mag_hb_init
 * @Desc:   开启tuya_sdk的心跳检测机制，如果开启，tuya_sdk将会每3s
 *          检测网关下所有子设备是否刷新心跳在超时时间内
 * @Param:  hb_send_cb，通知网关发送心跳给子设备
 *          tuya_sdk会在子设备超时时间范围内最多调用3次这个函数
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_sys_mag_hb_init(IN CONST DEV_HEARTBEAT_SEND_CB hb_send_cb);
```

#### tuya_iot_set_dev_hb_timeout_cfg

说明： 设置心跳检测超时时间,时间 ≥ 60s

```c
/***********************************************************
 * @Function:tuya_iot_set_dev_hb_timeout
 * @Desc:   设置子设备心跳超时时间
 * @Param:  dev_id， 子设备的设备id
 * @Param:  hb_timeout_time, 超时时间，单位s, 范围[60,0xffffffff)
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
#define tuya_iot_set_dev_hb_timeout(dev_id, hb_timeout_time) \
    tuya_iot_set_dev_hb_timeout_cfg(dev_id, hb_timeout_time, 2)
OPERATE_RET tuya_iot_set_dev_hb_timeout_cfg(IN CONST CHAR_T *dev_id,IN CONST TIME_S hb_timeout_time, IN CONST UINT_T max_resend_times);
```

#### tuya_iot_fresh_dev_hb

说明： 网关在收到子设备心跳包或数据后，调用刷新心跳时间

```c
/***********************************************************
 * @Function:tuya_iot_fresh_dev_hb
 * @Desc:   刷新子设备心跳时间
 * @Param:  dev_id， 子设备的设备id
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_fresh_dev_hb(IN CONST CHAR_T *dev_id);
```
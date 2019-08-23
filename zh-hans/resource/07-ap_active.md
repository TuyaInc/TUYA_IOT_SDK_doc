### AP配网模式

说明：设备开启默认热点, 手机搜索发现到热点后连接到设备热点，手机将连接公网的路由器热点的SSID/密码发送给WiFi设备，然后WiFi设备使用手机发送过来的SSID/密码连接路由器

### AP配网接口调用图

```sequence
Title: Device active in ap mode

participant TuyaApp
participant Device
participant tuya_sdk
participant TuyaCloud

tuya_sdk->Device: hwl_wf_wk_mode_set(WWM_SOFTAP)
tuya_sdk->Device: hwl_wf_ap_start
Note over TuyaApp:用户选择Ap模式添加设备\n输入路由器的ssid和passwd
TuyaApp-->tuya_sdk:连接设备热点，广播{ssid,passwd,token} 
tuya_sdk->Device:hwl_wf_ap_stop
tuya_sdk->Device: hwl_wf_wk_mode_set(WWM_STATION)
tuya_sdk->Device: hwl_wf_station_connect(ssid,passwd)
Device-->tuya_sdk: return OPRT_OK
tuya_sdk->Device: hwl_wf_station_stat_get
Note over tuya_sdk: 每隔1s查询一次网络状态
Device-->tuya_sdk: WSS_GOT_IP
tuya_sdk->TuyaCloud: 请求设备激活
TuyaCloud-->tuya_sdk: 设备激活成功
tuya_sdk->Device: __soc_dev_net_status_cb(STAT_CLOUD_CONN)\n通知设备配网成功
TuyaApp->TuyaCloud:刷新设备列表
TuyaCloud-->TuyaApp:设备列表新增设备
```
### 用户需实现的接口说明

说明：如下接口涉及wifi网卡操作，请根据设备系统实现，demo里的实现仅供参考，未必适配你的设备；tuya_sdk未对wifi网卡驱动层做限制，比如没有要求必须支持ifconfig命令。
接口位置：tuya_iot_sdk/demo_soc_dev_wifi/tuya_iot_wifi_net.c文件中

- [hwl_wf_wk_mode_set](wifi_api.md#hwlwfwkmodeset)
- [hwl_wf_wk_mode_get](wifi_api.md#hwlwfwkmodeget)
- [hwl_wf_station_connect](wifi_api.md#hwlwfstationconnect)
- [hwl_wf_station_stat_get](wifi_api.md#hwlwfstationstatget)

#### hwl_wf_ap_start
```c
/***********************************************************
 * @Function:hwl_wf_ap_stop
 * @Desc:   开启设备热点
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk会调用，应用层需要根据设备实现
***********************************************************/
OPERATE_RET hwl_wf_ap_start(IN CONST WF_AP_CFG_IF_S *cfg)
{
    PR_DEBUG("Start AP SSID:%s", cfg->ssid);
    if(cfg->passwd != NULL){
        PR_DEBUG("PASSWD:%s", cfg->passwd);
    }
    // UserTODO

    return OPRT_OK;
}
```

#### hwl_wf_ap_stop
```c
/***********************************************************
 * @Function:hwl_wf_ap_stop
 * @Desc:   关闭设备热点
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk会调用，应用层需要根据设备实现
***********************************************************/
OPERATE_RET hwl_wf_ap_stop(VOID)
{
    PR_DEBUG("Stop Ap Mode");
    // UserTODO

    return OPRT_OK;
}
```
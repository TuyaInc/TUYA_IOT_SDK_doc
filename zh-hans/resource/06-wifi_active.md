## WiFi设备配网

涉及到操作设备的底层接口，sdk将函数体定义好，具体需要开发者根据设备实现，sdk需要时会调用函数；
函数位置：tuya_iot_sdk/demo_soc_dev_wifi/tuya_iot_wifi_net.c文件中，文件中接口实现供参考，需要可以根据设备调整

### wifi配网支持模式设置

WiFi设备配网主要有Smart模式和AP模式两种, Smart配网模式需要设备wifi网卡支持sniffer模式。

[配网支持模式设置](05-device_init.md#tuyaiotwfsocinit)

配网模式切换说明：

1. WF_START_AP_ONLY, 调用tuya_iot_wf_gw_unactive，重启tuya_sdk后进入AP配网模式；
2. WF_START_SMART_ONLY, 调用tuya_iot_wf_gw_unactive，重启tuya_sdk后进入Smart配网模式；
3. WF_START_AP_FIRST, 设备已连接到涂鸦云情况下，第一次调用tuya_iot_wf_gw_unactive，重启tuya_sdk后进入AP配网模式；第二次调用进入Smart配网模式;如此循环切换
4. WF_START_SMART_FIRST, 设备已连接到涂鸦云情况下，第一次调用tuya_iot_wf_gw_unactive，重启tuya_sdk后进入Smart配网模式；第二次调用进入Ap配网模式;如此循环切换

### 设备进入配网模式流程

> [!NOTE]
> 说明：tuya_sdk会开启多线程，进入配网模式生效必须重启进程，不可只重启主线程。

```sequence
Title: 
participant Device
participant tuya_sdk

Device->tuya_sdk: call tuya_iot_wf_gw_unactive
tuya_sdk-->Device: return OPRT_OK
tuya_sdk->Device: __soc_dev_reset_req_cb
Device->tuya_sdk: 重启 tuya_sdk 进程
tuya_sdk-->Device: 进入配网模式
Device->tuya_sdk: tuya_iot_wf_fast_get_nc_type
tuya_sdk-->Device: 返回当前配网模式
Device-->Device: 配网LED闪烁,可以添加设备
```
### 设备重置

#### tuya_iot_wf_gw_unactive

```c
/***********************************************************
 * @Function:tuya_iot_wf_gw_unactive
 * @Desc:   重置设备，解除与涂鸦云的绑定
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_wf_gw_unactive(VOID);
```
在下面两种情况下，应用层需要调用 tuya_iot_wf_gw_unactive

1. 设备没有连接到涂鸦云，调用以进入配网模式 或者切换配网模式在AP和Smart之间。
2. 设备已连接到涂鸦云，调用以移除设备，切换绑定到其他用户。

#### __soc_dev_reset_req_cb
```c
/***********************************************************
 * @Function:__soc_dev_reset_req_cb
 * @Desc:   sdk进程重启请求，由tuya_sdk回调，应用层处理
 * @Param:  type，通知应用层网关重置类型，如下说明：
            GW_LOCAL_RESET_FACTORY，本地恢复出厂设置
            GW_REMOTE_UNACTIVE, TuyaApp端移除网关
            GW_LOCAL_UNACTIVE, 本地移除网关
            GW_REMOTE_RESET_FACTORY, 远程恢复出厂设置
            GW_RESET_DATA_FACTORY, 云端和本地数据不一致
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   必须重启整个进程，不可只取消线程，tuya_sdk会创建多线程
***********************************************************/
STATIC VOID __soc_dev_reset_req_cb(GW_RESET_TYPE_E type)
{
    PR_DEBUG("SOC Rev Reset Req %d", type);
    if(type == GW_RESET_DATA_FACTORY){
        //该设备之前被恢复过出厂设置，是否需要进行本地用户数据清理
        return;
    }
    // UserTODO 重启sdk线程
}
```

**说明：**
__soc_dev_reset_req_cb(GW_RESET_DATA_FACTORY)
主要是用来解决云端数据状态和本地数据状态不匹配的问题。
比如设备先在本地移除，然后APP上进行了恢复出厂设置操作，此时如果设备重新配网，SDK会告知应用该设备之前被恢复过出厂设置，需要进行本地数据清理，您这边如果没有本地数据需要清理，可以直接返回，无需进行后续的本地重置处理。

### 配网模式获取

用途：做配网LED闪烁，以表明设备处于AP配网模式还是Smart配网模式；用户在TuyaApp选择对应入口添加设备；

```c
/***********************************************************
 * @Function:tuya_iot_wf_fast_get_nc_type
 * @Desc:   获取tuya_sdk保存的配网模式字段值
 * @Param:  nc_type, 说明如下
            GWNS_FAST_LOWPOWER      0-关闭配网
            GWNS_FAST_UNCFG_SMC     1-Smart配网模式
            GWNS_FAST_UNCFG_AP      2-Ap配网模式
            GWNS_FAST_UNCFG_NORMAL  3-工作模式
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk进程重启后，应用层调用获取，做设备配网指示
***********************************************************/
OPERATE_RET tuya_iot_wf_fast_get_nc_type(GW_WF_NWC_FAST_STAT_T *nc_type)

//使用参考
GW_WF_NWC_FAST_STAT_T wf_nwc_fast_stat;
OPERATE_RET op_ret = tuya_iot_wf_fast_get_nc_type(&wf_nwc_fast_stat);
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_wf_fast_get_nc_type op_ret:%d",op_ret);
}
if(wf_nwc_fast_stat == GWNS_FAST_UNCFG_SMC){
    // tuya-sdk is in Smart distribution mode
}
else if(wf_nwc_fast_stat == GWNS_FAST_UNCFG_AP){
    // tuya-sdk is in AP distribution mode
}
```


#### 配网结果回调


[__soc_dev_net_status_cb](05-device_init.md#socdevnetstatuscb)

__soc_dev_net_status_cb(STAT_CLOUD_CONN) 为配网成功 或 设备成功连接到涂鸦云
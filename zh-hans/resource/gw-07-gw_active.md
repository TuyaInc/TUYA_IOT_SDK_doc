## 网关配网

  这里的网关配网是指有线配网,不用输入路由器的热点名称和密码，设备已连接外网；
  
  wifi网关配网请参考：[wifi设备配网](06-wifi_active.md#wifi设备配网)

- 先完成如下接口实现：

```c
// 获取有线网卡的ip地址
OPERATE_RET hwl_bnw_get_ip(OUT NW_IP_S *ip)
{
    // UserTODO
    strcpy(ip->ip, "192.168.12.102");
    return OPRT_OK;
}
// 硬件是否连接外网
BOOL_T hwl_bnw_station_conn(VOID)
{
    // UserTODO
    return TRUE;
}
```

### 配网数据交互图

```sequence
Title:wired gateway active

participant TuyaApp
participant gateway
participant tuya_sdk
participant TuyaCloud

gateway->tuya_sdk:tuya_iot_gw_unactive
tuya_sdk-->gateway:return OPRT_OK
tuya_sdk->gateway: __gw_dev_restart_req_cb(GW_LOCAL_UNACTIVE)
gateway->tuya_sdk: restarts tuya_sdk process
tuya_sdk-->gateway: enters the distribution mode.
Note over gateway: 发送广播包
Note over TuyaApp: 用户添加网关\n开始搜索设备
TuyaApp->tuya_sdk: 搜索到设备，建立连接，发送配网token
tuya_sdk->TuyaCloud:请求激活设备
TuyaCloud-->tuya_sdk:激活成功
tuya_sdk->gateway: __gw_dev_net_status_cb(GB_STAT_CLOUD_CONN)\n通知设备上线成功
TuyaApp->TuyaCloud:刷新设备列表
TuyaCloud-->TuyaApp:返回设备信息
```

### 接口说明

#### tuya_iot_gw_unactive
```c
/***********************************************************
 * @Function:tuya_iot_gw_unactive
 * @Desc:   用于应用层调用，重置网关设备
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   初始化函数完成后调用
***********************************************************/
OPERATE_RET tuya_iot_gw_unactive(VOID)
```

#### __gw_dev_restart_req_cb
```c
/***********************************************************
 * @Function:__gw_dev_restart_req_cb
 * @Desc:   网关进程重启请求，由tuya_sdk回调，应用层处理
 * @Param:  type，通知应用层网关重置类型，如下说明：
            GW_LOCAL_RESET_FACTORY，本地恢复出厂设置
            GW_REMOTE_UNACTIVE, TuyaApp端移除网关
            GW_LOCAL_UNACTIVE, 本地移除网关
            GW_REMOTE_RESET_FACTORY, 远程恢复出厂设置
            GW_RESET_DATA_FACTORY, 云端和本地数据不一致
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   必须重启整个进程，不可只取消线程，tuya_sdk会创建多线程
***********************************************************/
STATIC VOID __gw_dev_restart_req_cb(GW_RESET_TYPE_E type)
{
    PR_DEBUG("GW Rev Restart Req:%d",type);
    if(type == GW_RESET_DATA_FACTORY){
        //该设备之前被恢复过出厂设置，是否需要进行本地用户数据清理
        return;
    }
    // UserTODO 重启sdk线程
}
```
> [!NOTE]
> 说明：
    __gw_dev_restart_req_cb(GW_RESET_DATA_FACTORY)
    主要是用来解决云端数据状态和本地数据状态不匹配的问题。
    比如设备先在本地移除，然后APP上进行了恢复出厂设置操作，此时如果设备重新配网，SDK会告知应用该设备之前被恢复过出厂设置，需要进行本地数据清理，您这边如果没有本地数据需要清理，可以直接返回，无需进行后续的本地重置处理。

__gw_dev_net_status_cb， 请参考：[网关初始化](gw-05-gw_init.md#gwdevnetstatuscb)
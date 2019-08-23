## 公共函数

涉及到操作设备的底层接口，sdk将函数体定义好，具体需要开发者根据设备实现，sdk需要时会调用函数；
函数位置：tuya_iot_sdk/demo_soc_dev_wifi/tuya_iot_wifi_net.c文件中，文件中接口实现供参考，需要可以根据设备调整

### hwl_wf_station_connect

```c
/**
 * @Function: hwl_wf_station_connect
 * @Description: sdk收到app下发的路由器的ssid和passwd，通知应用层去连接路由器.
 * @Param: ssid && passwd, The router's wifi name and password
 * @Return: 这里直接返回OPRT_OK，设备连接路由器是否成功通过hwl_wf_station_stat_get函数通知tuya-sdk
 * @Note:    1. tuya-sdk将会调用，当设备收到手机app下发的配网信息时
             2. 设备配网成功后，设备断电重启，tuya-sdk会调用以连接路由器，让设备上线
 */
OPERATE_RET hwl_wf_station_connect(IN CONST CHAR_T *ssid,IN CONST CHAR_T *passwd)
{
    PR_DEBUG("STA Con AP ssid:%s passwd:%s", ssid, passwd);
    // UserTODO

    return OPRT_OK;
}
```

### hwl_wf_station_stat_get
```c
/**
 * @Function: hwl_wf_station_stat_get
 * @Description: 设备网络状态获取
 * @Param: OUT WF_STATION_STAT_E *stat
 *         1. WSS_GOT_IP    设备连接外网成功，
 *            tuya-sdk拿到这个状态后会连接涂鸦云上线或激活
 *         2. WSS_CONN_FAIL 设备连接外网失败
 * @Return: tuya-sdk会调用此函数获取设备的网络状态
 */
OPERATE_RET hwl_wf_station_stat_get(OUT WF_STATION_STAT_E *stat)
{
    // UserTODO
    *stat = ?;
    return OPRT_OK;
}
```

### hwl_wf_station_get_conn_ap_rssi

```c
/**
 * @Function: hwl_wf_station_get_conn_ap_rssi
 * @Description: 手机app获取设备信号强度时，tuya-sdk调用
 * @Param: OUT SCHAR_T *rssi
 * @Return:  OPRT_OK: success  Other: fail
 */
OPERATE_RET hwl_wf_station_get_conn_ap_rssi(OUT SCHAR_T *rssi)
{
    *rssi = ?;
    return OPRT_OK;
}
```

### hwl_wf_station_disconnect

```c
/**
 * @Function: hwl_wf_station_disconnect
 * @Description: tuya-sdk请求设备断开和路由器的连接；
 *               1. 重置配网后会，tuya-sdk会调用
 * @Param: VOID
 * @Return: void
 */
OPERATE_RET hwl_wf_station_disconnect(VOID)
{
    PR_DEBUG("Disconnect WIFI Conn");
    // UserTODO

    return OPRT_OK;
}
```

### hwl_wf_get_ip
```c
/**
 * @Function: hwl_wf_get_ip
 * @Description:  get wifi ip info.when wifi works in
 *                ap+station mode, wifi has two ips.
 * @Param: wf: wifi function type
 * @Param: ip: the ip addr info
 * @Return: OPRT_OK: success  Other: fail
 */
OPERATE_RET hwl_wf_get_ip(IN CONST WF_IF_E wf,OUT NW_IP_S *ip)
{
     // UserTODO
    return OPRT_OK;
}
```

### hwl_wf_wk_mode_set
```c
/***********************************************************
 * @Function:hwl_wf_wk_mode_set
 * @Desc:   设置设备wifi网卡工作模式
 * @Param:  mode,
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk会调用，应用层需要根据设备实现
 * @Note:   用户需实现 station/ap/sniffer三种模式切换的实现，
 *          Smart配网模式下，需要支持切换sniffer模式
***********************************************************/
OPERATE_RET hwl_wf_wk_mode_set(IN CONST WF_WK_MD_E mode)
{
    // UserTODO
    switch (mode)
    {
        case WWM_LOWPOWER:
        {
            //关闭配网模式
            break;
        }
        case WWM_SNIFFER:
        {
            break;
        }
        case WWM_STATION:
        {
            break;
        }
        case WWM_SOFTAP:
        {
            break;
        }
        case WWM_STATIONAP:
        {//reserved
            break;
        }
        default:
        {
            break;
        }
    }
    PR_DEBUG("WIFI Set Mode:%d", mode);
    return OPRT_OK;
}
```
### hwl_wf_wk_mode_get
```c
/***********************************************************
 * @Function:hwl_wf_wk_mode_get
 * @Desc:   获取设备wifi网卡工作模式
 * @Param:  mode,
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk会调用，应用层需要根据设备实现
***********************************************************/
OPERATE_RET hwl_wf_wk_mode_get(OUT WF_WK_MD_E *mode)
{
    // UserTODO
    *mode = ? ;
    PR_DEBUG("WIFI Get Mode:%d", *mode);
    return OPRT_OK;
}
```




## 设备初始化

网关产品key信息获取，请参考[创建产品](New_product.md)

### tuya_iot_wf_soc_dev_init
```c
/***********************************************************
 * @Function:tuya_iot_wf_soc_dev_init
 * @Desc:   wifi设备初始化接口
 * @Param:  cfg,选择GWCM_OLD
 * @Param:  start_mode, 配网支持模式选择
 *          WF_START_AP_ONLY: 只支持AP配网模式
 *          WF_START_SMART_ONLY: 只支持Smart配网模式
 *          WF_START_AP_FIRST: 同时支持AP和Smart两种模式，配网成功后重置进入AP模式
 *          WF_START_SMART_FIRST: 同时支持AP和Smart两种模式，配网成功后重置进入Smart模式
 * @Param:  cbs, sdk用户回调
 * @Param:  product_key,设备的产品key信息
 * @Param:  wf_sw_ver,设备的固件版本 格式:xx.xx.xx (0<=x<=9)
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
#define tuya_iot_wf_soc_dev_init(cfg, start_mode,cbs,product_key,wf_sw_ver) \
        tuya_iot_wf_soc_dev_init_param(cfg, start_mode,cbs,NULL,product_key,wf_sw_ver)

__TUYA_IOT_WIFI_API_EXT \
OPERATE_RET tuya_iot_wf_soc_dev_init_param(IN CONST GW_WF_CFG_MTHD_SEL cfg, IN CONST GW_WF_START_MODE start_mode,
                                     IN CONST TY_IOT_CBS_S *cbs,IN CONST CHAR_T *firmware_key,
                                     IN CONST CHAR_T *product_key,IN CONST CHAR_T *wf_sw_ver);
```
#### 接口使用说明
```c
#define USER_SW_VER  "1.0.0"  // wifi设备固件版本，用于OTA管理

    TY_IOT_CBS_S iot_cbs = {
        __soc_dev_status_changed_cb,
        __soc_dev_rev_upgrade_info_cb,
        __soc_dev_reset_req_cb,
        __soc_dev_obj_dp_cmd_cb,
        __soc_dev_raw_dp_cmd_cb,
        __soc_dev_dp_query_cb,
        NULL,
    };
    op_ret = tuya_iot_wf_soc_dev_init(GWCM_OLD, WF_CFG_MODE_SELECT, &iot_cbs, PRODUCT_KEY, USER_SW_VER);
    if(OPRT_OK != op_ret) {
        PR_ERR("tuya_iot_wf_soc_dev_init err:%d",op_ret);
        return -4;
    }
    PR_NOTICE("tuya_iot_wf_soc_dev_init success");
```

#### sdk用户回调
- __soc_dev_status_changed_cb
- [__soc_dev_rev_upgrade_info_cb](11-soc_ota.md#socdevrevupgradeinfocb)
- [__soc_dev_reset_req_cb](06-wifi_active.md#socdevresetreqcb)
- [__soc_dev_obj_dp_cmd_cb](17-rev.md#socdevobjdpcmdcb)
- [__soc_dev_raw_dp_cmd_cb](17-rev.md#socdevrawdpcmdcb)

### tuya_iot_reg_get_nw_stat_cb
```c
/***********************************************************
 * @Function:tuya_iot_reg_get_nw_stat_cb
 * @Desc:   设备网络状态通知回调注册
 * @Param:  nw_stat_cb，回调函数名称
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk每隔1s查询一次状态，如果改变，通知应用层
***********************************************************/
OPERATE_RET tuya_iot_reg_get_nw_stat_cb(IN CONST GET_NW_STAT_CB nw_stat_cb);
```
#### 接口使用说明
```c
op_ret = tuya_iot_reg_get_nw_stat_cb(__soc_dev_net_status_cb);
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_reg_get_nw_stat_cb err:%d",op_ret);
    return -4;
}
PR_DEBUG("tuya_iot_reg_get_nw_stat_cb success");
```

#### __soc_dev_net_status_cb
```c
/***********************************************************
 * @Function:__soc_dev_net_status_cb
 * @Desc:   设备网络状态通知，由tuya_sdk回调通知应用层
 * @Param:  stat，网络状态参数，如下说明：
            STAT_LOW_POWER,     0-idle status,use to external config network
            STAT_UNPROVISION,   1-Smart配网模式
            STAT_AP_STA_UNCFG,  2-Ap配网模式
            STAT_AP_STA_DISC,   3-ap WIFI already config,station disconnect
            STAT_AP_STA_CONN,   4-ap station mode,station connect
            STAT_STA_DISC,      5-only station mode,disconnect
            STAT_STA_CONN,      6-设备连接路由器成功
            STAT_CLOUD_CONN,    7-设备成功连接涂鸦云
            STAT_AP_CLOUD_CONN, 8-设备成功连接涂鸦云，且ap热点开启，适用于支持sta+ap模式的设备
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
STATIC VOID __soc_dev_net_status_cb(IN CONST GW_WIFI_NW_STAT_E stat)
{
    PR_NOTICE("network status:%d", stat);
    //User TODO
}
```

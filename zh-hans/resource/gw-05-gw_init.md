
## 网关初始化
tuya最新的sdk支持网关产品上定义dp点

网关产品key信息获取，请参考[创建产品](02-New_product.md)

### tuya_iot_gw_init
```c
#include "tuya_iot_base_api.h"
/***********************************************************
 * @Function:tuya_iot_gw_init
 * @Desc:   有线网关初始化接口
 * @Param:  cbs,    sdk用户回调
 * @Param:  gw_cbs, 子设备管理用户回调
 * @Param:  product_key,网关的产品key信息
 * @Param:  sw_ver,网关的固件版本 格式:xx.xx.xx (0<=x<=9)
 * @Param:  attr,网关属性数组
 * @Param:  attr_num,网关属性长度
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_gw_init(IN CONST TY_IOT_CBS_S *cbs,IN CONST TY_IOT_GW_CBS_S *gw_cbs,\
                             IN CONST CHAR_T *product_key,IN CONST CHAR_T *sw_ver,\
                             IN CONST GW_ATTACH_ATTR_T *attr,IN CONST UINT_T attr_num);
```
接口使用说明：
```c
#define USER_DEV_IN_GW_SW_VER  "1.0.0"  // 网关内部通讯模块固件版本，用于OTA管理

TY_IOT_CBS_S iot_cbs = {
        __gw_dev_status_changed_cb,  // 
        __gw_dev_rev_upgrade_info_cb,// 
        __gw_dev_restart_req_cb,     // 
        __gw_dev_obj_dp_cmd_cb,      // 
        __gw_dev_raw_dp_cmd_cb,      // 
        __gw_dev_dp_query_cb,        // 不常用功能
        __dev_ug_inform_cb,          // 
        __dev_reset_cb,
    };

TY_IOT_GW_CBS_S gw_cbs = {
        __gw_permit_add_dev_cb,
        __gw_dev_del_cb,
        __gw_dev_grp_infm_cb,
        __gw_dev_scene_infm_cb,
        __gw_bind_dev_inform_cb,
    };

// 网关内部通讯模块属性，通讯类型+固件版本
GW_ATTACH_ATTR_T attr[] = {
    {GP_DEV_ZB, USER_DEV_IN_GW_SW_VER},
};

op_ret = tuya_iot_gw_init(&iot_cbs, &gw_cbs, GW_PRODUCT_KEY, USER_SW_VER, attr, \
                            sizeof(attr)/sizeof(GW_ATTACH_ATTR_T));
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_gw_init op_ret:%d",op_ret);
    return op_ret;
}
```

### SDK用户回调
- __gw_dev_rev_upgrade_info_cb [网关设备升级入口](gw-10-gw_ota.md#gwdevrevupgradeinfocb)
- __gw_dev_restart_req_cb [网关进程重启请求](gw-07-gw_active.md#gwdevrestartreqcb)
- __gw_dev_obj_dp_cmd_cb [dp指令下发入口(obj类型)](gw-17-rev.md#gwdevobjdpcmdcb)
- __gw_dev_raw_dp_cmd_cb [dp指令下发入口(raw类型)](gw-17-rev.md#gwdevrawdpcmdcb)
- __dev_ug_inform_cb [子设备升级入口](gw-11-sub_ota.md#devuginformcb)
- __dev_reset_cb [子设备复位回调](gw-08-sub_active.md#devresetcb)

### 子设备管理回调
- __gw_permit_add_dev_cb [子设备添加请求](gw-08-sub_active.md#gwpermitadddevcb)
- __gw_dev_del_cb [子设备解绑通知](gw-08-sub_active.md#gwdevdelcb)
- __gw_bind_dev_inform_cb [子设备绑定激活结果通知](gw-08-sub_active.md#gwbinddevinformcb)
- __gw_dev_grp_infm_cb [组操作](gw-13-grp_infm.md#gwdevgrpinfmcb)
- __gw_dev_scene_infm_cb [场景管理](gw-14-scene_infm.md#gwdevsceneinfmcb)

### tuya_iot_wf_gw_init
```c
#include "tuya_iot_wifi_api.h"
/***********************************************************
 * @Function:tuya_iot_wf_gw_init
 * @Desc:   wifi网关初始化接口
 * @Param:  cfg,默认为GWCM_OLD
 * @Param:  start_mode, wifi网关支持配网模式种类设置，AP或者Smart
 * @Param:  cbs,    sdk用户回调
 * @Param:  gw_cbs, 子设备管理用户回调
 * @Param:  product_key,网关的产品key信息
 * @Param:  wf_sw_ver,网关的固件版本 格式:xx.xx.xx (0<=x<=9)
 * @Param:  attr,网关属性数组
 * @Param:  attr_num,网关属性长度
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_wf_gw_init(IN CONST GW_WF_CFG_MTHD_SEL cfg, \
                                IN CONST GW_WF_START_MODE start_mode,\
                                IN CONST TY_IOT_CBS_S *cbs, IN CONST TY_IOT_GW_CBS_S *gw_cbs,
                                IN CONST CHAR_T *product_key, IN CONST CHAR_T *wf_sw_ver,
                                IN CONST GW_ATTACH_ATTR_T *attr, IN CONST UINT_T attr_num);
```

#### tuya_iot_reg_get_nw_stat_cb
```c
/***********************************************************
 * @Function:tuya_iot_reg_get_nw_stat_cb
 * @Desc:   网关网络状态通知回调注册
 * @Param:  nw_stat_cb，回调函数名称
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk每隔1s查询一次状态，如果改变，通知应用层
***********************************************************/
OPERATE_RET tuya_iot_reg_get_nw_stat_cb(IN CONST GET_NW_STAT_CB nw_stat_cb);
```
接口使用说明：
```c
op_ret = tuya_iot_reg_get_nw_stat_cb(__gw_dev_net_status_cb);
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_reg_get_nw_stat_cb err:%d",op_ret);
    return -4;
}
PR_DEBUG("tuya_iot_reg_get_nw_stat_cb success");
```

#### __gw_dev_net_status_cb
```c
/***********************************************************
 * @Function:__gw_dev_net_status_cb
 * @Desc:   网关网络状态通知，由tuya_sdk回调通知应用层
 * @Param:  stat，网络状态参数，如下说明：
            GB_STAT_LAN_UNCONN, 局域网离线
            GB_STAT_LAN_CONN, 局域网在线，配网状态
            GB_STAT_CLOUD_CONN， 涂鸦云在线，设备上线成功
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
STATIC VOID __gw_dev_net_status_cb(IN CONST GW_BASE_NW_STAT_T stat)
{
    PR_DEBUG("network status:%d", stat);
}
```
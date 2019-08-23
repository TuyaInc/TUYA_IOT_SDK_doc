## sdk初始化

### 接口说明

#### tuya_iot_init

```c
#include "tuya_iot_com_api.h"
/***********************************************************
 * @Function:tuya_iot_init
 * @Desc:   用于tuya iot 系统的初始化，必须最先调用
 * @Param:  fs_storge_path，为sdk分配可读写的文件目录字符串，长度小于110字节
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   此目录用于sdk存储运行过程中需要断电保存的数据
***********************************************************/
OPERATE_RET tuya_iot_init(IN CONST CHAR_T *fs_storge_path)
```

接口使用示例：

```c
#define CFG_STORAGE_PATH    "./"

OPERATE_RET op_ret = tuya_iot_init(CFG_STORAGE_PATH);
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_init err:%d PATH:%s", op_ret, CFG_STORAGE_PATH);
    return op_ret;
}
PR_NOTICE("tuya_iot_init success");
```
#### 日志管理
AddOutputTerm 和 SetLogManageAttr接口使用方法请参考：
[日志管理](log_manage.md)

#### tuya_iot_set_wf_gw_prod_info
```c
#include "tuya_iot_wifi_api.h"
/***********************************************************
 * @Function:tuya_iot_set_wf_gw_prod_info
 * @Desc:   设置设备授权信息(用于wifi设备)
 * @Param:  prod_info,授权信息 + 配网热点名称
 *          热点名称字符串长度小于16字节
 *          热点密码字符串长度小于16字节
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_set_wf_gw_prod_info(IN CONST WF_GW_PROD_INFO_S *wf_prod_info);
```
#### 接口使用说明
```c
// ap_ssid和ap_passwd参数可以NULL;
// ap_ssid=NULL时，默认热点名称为SmartLife-XXXX
#define ap_ssid     "TuyaSmart" // 长度不得大于16字节
#define ap_passwd   "12345678"  // 长度不得大于16字节

WF_GW_PROD_INFO_S prod_info = {UUID, AUTHKEY, ap_ssid, ap_passwd};
op_ret = tuya_iot_set_wf_gw_prod_info(&prod_info);
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_set_wf_gw_prod_info op_ret:%d", op_ret);
    return op_ret;
}
PR_NOTICE("tuya_iot_set_wf_gw_prod_info success");
```
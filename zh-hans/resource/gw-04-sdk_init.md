## sdk初始化

### 设备认证
设备的身份认证采用一机一密的方式，在设备上烧写设备的唯一的UUID、AUTHKEY，这种方式要求对设备的产线工具进行一定的修改，需要对每个设备烧写不同的UUID和AUTHKEY；
UUID和AUTHKEY成对出现，每一台设备都必须有自己uuid & authkey，且唯一，请向涂鸦项目经理申请几组用于调试。
在demo 中 PID，uuid & authkey
仅用作测试使用，不能用于实际产品，会导致后续产品不可用。PID
需要用户自行从涂鸦开发者平台申请。每一台设备都必须有自己uuid &
authkey，且唯一。

### 函数调用流程

使用sdk其他接口功能前，要现在线程中调用如下接口完成tuya_sdk初始化过程；

```uml
@startuml
title tuya_sdk初始化调用流程
start
:tuya_iot_init;
:AddOutputTerm(可选);
:SetLogManageAttr(可选);
if (wired gateway?) then (yes)
:tuya_iot_set_gw_prod_info;
:tuya_iot_gw_init;
else (no)
:tuya_iot_set_wf_gw_prod_info;
:tuya_iot_wf_gw_init;
endif
:tuya_iot_reg_get_nw_stat_cb;
stop
@enduml
```

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

#### tuya_iot_set_gw_prod_info
```c
#include "tuya_iot_base_api.h"
/***********************************************************
 * @Function:tuya_iot_set_gw_prod_info
 * @Desc:   设置设备授权信息(用于有线网关)
 * @Param:  prod_info,授权信息
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_set_gw_prod_info(IN CONST GW_PROD_INFO_S *prod_info);
```
接口使用说明：
```c
// UUID和AUTHKEY用于涂鸦云设备的安全认证，每个设备所用key均为唯一
#define UUID                "tuya19342bc6e2d80143"
#define AUTHKEY             "IbOFAiLzNAxgT84zd1mrjpy0sUSbJBt3"

GW_PROD_INFO_S prod_info = {UUID, AUTHKEY};
op_ret = tuya_iot_set_gw_prod_info(&prod_info);
if(OPRT_OK != op_ret) {
    PR_ERR("tuya_iot_set_gw_prod_info op_ret:%d", op_ret);
    return op_ret;
}
PR_NOTICE("tuya_iot_set_gw_prod_info success");
```

#### tuya_iot_set_wf_gw_prod_info
```c
#include "tuya_iot_wifi_api.h"
/***********************************************************
 * @Function:tuya_iot_set_wf_gw_prod_info
 * @Desc:   设置设备授权信息(用于wifi网关)
 * @Param:  prod_info,授权信息 + 配网热点名称
 *          热点名称字符串长度小于16字节
 *          热点密码字符串长度小于16字节
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET tuya_iot_set_wf_gw_prod_info(IN CONST WF_GW_PROD_INFO_S *wf_prod_info);
```
接口使用说明：
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

## 子设备配网

### 子设备配网数据交互图

```sequence
Title:sub device active

participant TuyaApp
participant sub_device
participant gateway
participant tuya_sdk
participant TuyaCloud

sub_device->gateway:重置子设备
gateway->tuya_sdk:tuya_iot_gw_unbind_dev
tuya_sdk-->gateway:return OPRT_OK
tuya_sdk->TuyaCloud:请求解绑设备
TuyaCloud-->tuya_sdk:解绑成功
gateway-->sub_device:OK
Note over TuyaApp: 添加子设备
TuyaApp->tuya_sdk:请求添加
tuya_sdk->gateway:__gw_permit_add_dev_cb(true)
gateway->sub_device:请求子设备信息
sub_device-->gateway:应答
gateway->tuya_sdk: tuya_iot_gw_bind_dev_attr
tuya_sdk-->gateway:return OPRT_OK
tuya_sdk->TuyaCloud:请求激活设备
TuyaCloud-->tuya_sdk:激活成功
tuya_sdk->gateway:__gw_bind_dev_inform_cb通知激活结果
TuyaApp->TuyaCloud:刷新设备列表
TuyaCloud-->TuyaApp:返回设备信息
```
说明：sub_device 和 gateway之间通信协议由用户实现

### 接口说明

#### tuya_iot_gw_unbind_dev
```c
#include "tuya_iot_com_api.h"
/***********************************************************
 * @Function:tuya_iot_gw_unbind_dev
 * @Desc:   网关请求解绑子设备
 * @Param:  id，子设备的设备id
 * @Return: TRUE: success  FALSE: fail，
***********************************************************/
OPERATE_RET tuya_iot_gw_unbind_dev(IN CONST CHAR_T *id);
```

#### __gw_permit_add_dev_cb
```c
/***********************************************************
 * @Function:__gw_permit_add_dev_cb
 * @Desc:   子设备添加请求
 * @Param:  tp，子设备类型
            DEV_ZB_SNGL     2   // zigbee single device
            DEV_RF433_SNGL  3   // rf433 single device
            DEV_BLE_SNGL    4   // ble single device
 * @Param:  permit,是否允许添加子设备
 * @Param:  timeout,配网超时时间
 * @Return: TRUE: success  FALSE: fail，
***********************************************************/
STATIC BOOL_T __gw_permit_add_dev_cb(IN CONST GW_PERMIT_DEV_TP_T tp,IN CONST BOOL_T permit,IN CONST UINT_T timeout)
```
接口实现参考：
```c
STATIC BOOL_T __gw_permit_add_dev_cb(IN CONST GW_PERMIT_DEV_TP_T tp,IN CONST BOOL_T permit,IN CONST UINT_T timeout)
{
    PR_DEBUG("add dev permit changed. type:%d permit:%s timeout:%u", tp, permit?"true":"false", timeout);

    // 这里为说明，子设备信息传入固定值，实际要从子设备获取
    if(permit == true){
        OPERATE_RET op_ret = tuya_iot_gw_bind_dev_attr(GP_DEV_ZB, 0x12345678, "changcheng05", "okppr6u7", "1.0.0",NULL,0);
        if(op_ret != OPRT_OK) {
            PR_ERR("add subdevice fail. %d", op_ret);
            return false;
        }
    }
    else{
        // 子设备关闭允许入网
    }

   return true;
}
```

#### tuya_iot_gw_bind_dev_attr
```c
/***********************************************************
 * @Function:tuya_iot_gw_bind_dev_attr
 * @Desc:   绑定子设备，应用层主动调用
 * @Param:  tp,子设备类型
 * @Param:  uddd, sub-device detail type
 * @Param:  id,子设备的dev_id，自定义，strlen(id) <= 25 
 * @Param:  pk,子设备的产品KEY，从iot.tuya.com创建产品后获得
 * @Param:  ver,子设备的固件版本,格式:xx.xx.xx (0<=x<=9)
 * @Param:  attr,sub-device mcu versions,无mcu时传入NULL
 * @Param:  attr_num,sub-device mcu num,无mcu时传入0
 * @Return: TRUE: success  FALSE: fail，
***********************************************************/
OPERATE_RET tuya_iot_gw_bind_dev_attr(IN CONST GW_PERMIT_DEV_TP_T tp,IN CONST USER_DEV_DTL_DEF_T uddd,\
                                      IN CONST CHAR_T *id,IN CONST CHAR_T *pk,IN CONST CHAR_T *ver, \
                                      IN CONST GW_ATTACH_ATTR_T *attr,IN CONST UINT_T attr_num);
```

#### __gw_bind_dev_inform_cb
```c
/***********************************************************
 * @Function:__gw_bind_dev_inform_cb
 * @Desc:   网关子设备绑定激活结果通知
 * @Param:  dev_id，子设备的设备id
 * @Param:  op_ret,绑定结果，OPRT_OK: success  Other: fail
***********************************************************/
STATIC VOID __gw_bind_dev_inform_cb(IN CONST CHAR_T *dev_id, IN CONST OPERATE_RET op_ret)
```
#### __gw_dev_del_cb
```c
/***********************************************************
 * @Function:__gw_dev_del_cb
 * @Desc:   子设备解绑通知
 * @Param:  dev_id，子设备的设备id
 * @Param:  op_ret,绑定结果，OPRT_OK: success  Other: fail
***********************************************************/
STATIC VOID __gw_dev_del_cb(IN CONST CHAR_T *dev_id)
{
    PR_DEBUG("device delete cb. dev_id:%s", dev_id);
}
```
#### __dev_reset_cb
```c
/***********************************************************
 * @Function:__dev_reset_cb
 * @Desc:   子设备复位回调
 * @Param:  type，如下说明：
            DEV_REMOTE_RESET_FACTORY， 子设备被从TuyaApp恢复出厂设置，请移除设备并清除本地数据；
            DEV_RESET_DATA_FACTORY, 需要清理本地数据；
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
STATIC VOID __dev_reset_cb(IN CONST CHAR_T *dev_id,IN DEV_RESET_TYPE_E type);
{
    PR_DEBUG("sub device reset cb. dev_id:%s, type:%d", dev_id,type);
}
```
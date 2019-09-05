## 子设备固件升级

### 数据交互图
说明：
1. sub_device 和 gateway设备层的通信由客户实现，这里只简单说明；
2. 升级进度上报完全可由用户控制，这里只在网关给子设备发送固件包的过程中更新上报进度；
3. 涂鸦智能App固件升级超时60s机制：每收到设备的一包进度更新消息，重新计时；应用层可通过控制升级进度上报以延长TuyaApp超时
```uml
@startuml
@startuml
title sub_device ota simple

participant TuyaApp
participant sub_device
participant gateway
participant tuya_sdk
participant TuyaCloud

Note over TuyaCloud:用户上传配置固件包
Note over TuyaApp:用户启动升级
TuyaApp->tuya_sdk:通知升级
tuya_sdk->TuyaCloud:获取设备升级信息
TuyaCloud-->tuya_sdk:有升级任务
tuya_sdk->gateway:__dev_ug_inform_cb
gateway-[#FF0000]>tuya_sdk: tuya_iot_upgrade_dev_notify，注册回调
Note over tuya_sdk:创建升级线程,dp下发通道关闭
tuya_sdk->TuyaCloud:更新设备升级状态为升级中
TuyaCloud-->tuya_sdk:success
loop Downloading
tuya_sdk->TuyaCloud:Download
TuyaCloud-->tuya_sdk:
tuya_sdk->gateway:__get_file_data_cb
gateway-->tuya_sdk:return OPRT_OK
Note over gateway:接收保存固件
end
Note over tuya_sdk:退出升级线程,dp下发通道打开
tuya_sdk->gateway:__upgrade_notify_cb通知下载结果
loop Send to sub_device
gateway->sub_device:发送固件包
sub_device-->gateway:ACK
gateway->tuya_sdk:tuya_iot_dev_upgd_progress_rept
tuya_sdk-->TuyaCloud:
TuyaCloud->TuyaApp:推送升级进度
end
Note over sub_device:固件包接收完成,安装固件
sub_device->gateway:上报固件版本号
gateway->tuya_sdk:tuya_iot_gw_subdevice_update
tuya_sdk->TuyaCloud:更新设备版本号
Note over TuyaCloud:校验版本号
TuyaCloud-->TuyaApp:版本号匹配成功，更新设备升级状态为升级完成，推送升级成功消息
Note over TuyaApp:升级成功
@enduml
```

### 相关接口说明

#### __dev_ug_inform_cb
```c
/***********************************************************
 * @Function:__dev_ug_inform_cb
 * @Desc:   子设备升级入口
 * @Param:  dev_id,
 *          1. 如果升级的是子设备，dev_id = sub_deviceid;
 *          2. 如果升级的是子设备的MCU, dev_id = NULL;
 * @Param:  fw,固件信息结构体
 * @Param:  void
***********************************************************/
VOID __dev_ug_inform_cb(IN CONST CHAR_T *dev_id,IN CONST FW_UG_S *fw)
{
    PR_DEBUG("DevID Rev Upgrade Info");

    FILE *p_upgrade_fd = fopen(SOC_OTA_FILE, "w+b");
    if(NULL == p_upgrade_fd){
        PR_ERR("open upgrade file fail. upgrade fail %s", SOC_OTA_FILE);
        return;
    }
#if 0  // sdk上报进度
    OPERATE_RET op_ret = tuya_iot_upgrade_dev(dev_id, fw, __get_file_data_cb, __upgrade_notify_cb, p_upgrade_fd);
#else  // 关闭进度上报，用应用层上报
    OPERATE_RET op_ret = tuya_iot_upgrade_dev_notify(dev_id, fw, __get_file_data_cb, __upgrade_notify_cb, p_upgrade_fd,FALSE,0);
#endif 
    if(OPRT_OK != op_ret) {
        PR_ERR("tuya_iot_upgrade_gw err:%d",op_ret);
    }
}
```

#### __get_file_data_cb & __upgrade_notify_cb

网关和子设备不能同时进行固件升级，可和网关共用相同回调

请参考：[__get_file_data_cb](10-gw_ota.md#getfiledatacb)

请参考：[__upgrade_notify_cb](10-gw_ota.md#upgradenotifycb)

#### tuya_iot_upgrade_dev_notify
```c
/***********************************************************
 * @Function:tuya_iot_upgrade_dev_notify
 * @Desc:    注册回调，启动子设备或MCU固件升级线程
 * @Param:   devid,
 *           if upgrade sub-device, then devid = sub-device_id
 *           if upgrade soc/mcu, then devid = NULL
 * @Param:   fw, 固件信息
 * @Param:   get_file_cb, 接收固件包回调
 * @Param:   upgrd_nofity_cb, 固件包下载结果通知回调
 * @Param:   pri_data, private param of get_file_cb && upgrd_nofity_cb
 * @Param:   notify,是否由tuya_sdk上报升级进度
 * @Param:   download_buf_size, 下载最大缓存，单位字节
 * @Return:  TRUE: success  FALSE: fail，
***********************************************************/
#define tuya_iot_upgrade_dev(devid, fw, get_file_cb, upgrd_nofity_cb, pri_data) \
    tuya_iot_upgrade_dev_notify(devid, fw, get_file_cb, upgrd_nofity_cb, pri_data, TRUE, 0)
OPERATE_RET tuya_iot_upgrade_dev_notify(IN CONST CHAR_T *devid,
                                        IN CONST FW_UG_S *fw, \
                                        IN CONST GET_FILE_DATA_CB get_file_cb,\
                                        IN CONST UPGRADE_NOTIFY_CB upgrd_nofity_cb,\
                                        IN CONST PVOID_T pri_data,\
                                        BOOL_T notify, UINT_T download_buf_size);

```

#### tuya_iot_dev_upgd_progress_rept
```c
/***********************************************************
 * @Function:tuya_iot_dev_upgd_progress_rept
 * @Desc:   上报升级进度
 * @Param percent, 升级进度，范围(0,100)
 * @Param devid, 子设备时，传入子设备的devid; 网关时，传入NULL
 * @Return: TRUE: success  FALSE: fail，
 * @Note 子设备过程中，应用层主动调用
 * @Note 网关和子设备升级共用此接口，通过devid参数区分
***********************************************************/
OPERATE_RET tuya_iot_dev_upgd_progress_rept(IN CONST UINT_T percent, \
                                            IN CONST CHAR_T *devid,  \
                                            IN CONST DEV_TYPE_T tp);
```

#### tuya_iot_gw_subdevice_update
```c
/***********************************************************
 * @Function:tuya_iot_gw_subdevice_update
 * @Desc:   更新子设备固件版本号
 * @Param:  id，子设备id
 * @Param:  ver,子设备的固件版本,格式:xx.xx.xx (0<=x<=9)
 * @Param:  attr,sub-device mcu versions,无mcu时传入NULL
 * @Param:  attr_num,sub-device mcu num,无mcu时传入0
 * @Param:  is_force,传入0
 * @Return: TRUE: success  FALSE: fail，
***********************************************************/
OPERATE_RET tuya_iot_gw_subdevice_update_versions(IN CONST CHAR_T *id,IN CONST CHAR_T *ver,\
                                                  IN CONST GW_ATTACH_ATTR_T *attr,IN CONST UINT_T attr_num,\
                                                  IN CONST BOOL_T is_force);
#define tuya_iot_gw_subdevice_update(id, ver) \
        tuya_iot_gw_subdevice_update_versions(id, ver, NULL, 0, 0)
```

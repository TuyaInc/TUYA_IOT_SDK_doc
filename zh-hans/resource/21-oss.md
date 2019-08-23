## OSS对象存储服务

此服务目前用于激光扫地机品类，用来上报数据量较大的全量激光地图数据；

### 链路图

```uml
@startuml
title OSS
participant Device
participant tuya_sdk
participant TuyaCloud
participant TuyaApp

Device->tuya_sdk:tuya_iot_upload_layout_buffer
tuya_sdk->TuyaCloud:上报数据到oss
TuyaCloud-->tuya_sdk:success
tuya_sdk->TuyaCloud:PUBLISH(Qos0,Msg601)
tuya_sdk-->tuya_sdk:Delete(Msg)
TuyaCloud->TuyaApp:PUBLISH(Qos0,Msg601)
TuyaApp->TuyaCloud:下载地图数据
TuyaCloud-->TuyaApp:success
@enduml
```

### 接口说明

#### tuya_iot_upload_layout_buffer

```c
/***********************************************************
*  Function: tuya_iot_upload_layout_buffer
*  Desc:  sweeper function. upload cleaner map info
*  Input: map_id
*  Input: buffer
*  Input: len
*  Return: OPERATE_RET
***********************************************************/
#define tuya_iot_upload_layout_buffer(map_id, buffer, len) \
tuya_iot_map_cleaner_upload_buffer(map_id, buffer, len, "layout/lay.bin", UP_CLEANER_MAP)

OPERATE_RET tuya_iot_map_cleaner_upload_buffer(IN CONST INT_T map_id, IN CONST BYTE_T *buffer, IN CONST UINT_T len, \
                                               IN CONST CHAR_T *cloud_file_name, IN CONST UP_MAP_TYPE_E map_type);
```

#### tuya_iot_upload_route_buffer


```c
/***********************************************************
*  Function: tuya_iot_upload_route_buffer
*  Desc:  sweeper function. upload cleaner map info
*  Input: map_id
*  Input: buffer
*  Input: len
*  Return: OPERATE_RET
***********************************************************/
#define tuya_iot_upload_route_buffer(map_id, buffer, len) \
    tuya_iot_map_cleaner_upload_buffer(map_id, buffer, len, "route/rou.bin", UP_CLEANER_PATH)

OPERATE_RET tuya_iot_map_cleaner_upload_buffer(IN CONST INT_T map_id, IN CONST BYTE_T *buffer, IN CONST UINT_T len, \
                                               IN CONST CHAR_T *cloud_file_name, IN CONST UP_MAP_TYPE_E map_type);
```

####　tuya_iot_map_record_upload_buffer

1. 激光扫地机清扫记录上报接口;
2. 

```c
/***********************************************************
*  Function: tuya_iot_map_record_upload_buffer
*  Desc:  sweeper function. upload record map info
*  Input: map_id
*  Input: buffer
*  Input: len
*  Input: cloud_file_name
*  Input: extend
*  Return: OPERATE_RET
***********************************************************/
#define tuya_iot_map_record_upload_buffer(map_id,buffer,len,cloud_file_name,extend)\
    upload_map_custom_buffer(map_id,buffer,len,cloud_file_name,0,FALSE,extend,TRUE)

OPERATE_RET upload_map_custom_buffer(IN CONST INT_T map_id, IN CONST BYTE_T *buf,IN CONST UINT_T len, \
                                     IN CONST CHAR_T *cloud_file_name, \
                                     IN CONST INT_T map_type, IN CONST BOOL_T send_mqtt, \
                                     IN CONST CHAR_T *extend_msg, IN CONST BOOL_T http_update);
```
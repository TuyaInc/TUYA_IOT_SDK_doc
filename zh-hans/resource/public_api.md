## 功能接口

### 获取网关或SOC设备id
```c
/***********************************************************
*  Function: tuya_iot_get_gw_id
*  Input: none
*  Output: none
*  Return: (CHAR_T *)->device id
***********************************************************/
CHAR_T *tuya_iot_get_gw_id(VOID);
```

### 获取设备位置信息
```c
/***********************************************************
*  Function: tuya_iot_get_location_info
*  Input: p_location
*  Output: p_location
*  Return: OPERATE_RET
***********************************************************/
OPERATE_RET tuya_iot_get_location_info(INOUT TY_LOCATION_INFO_S *p_location);
```

### 获取设备时区信息
```c
/***********************************************************
*  Function: tuya_iot_get_region_info
*  Input: none
*  Output: p_region_info
*  Return: OPERATE_RET
***********************************************************/
OPERATE_RET tuya_iot_get_region_info(INOUT TY_IOT_REGION_INFO_S *p_region_info);
```

### 取消升级
设备不满足升级条件，主动取消升级
```c
OPERATE_RET tuya_iot_refuse_upgrade(IN CONST FW_UG_S *fw, IN CONST CHAR_T *dev_id);
```

### 获取本地时间

说明：用于获取网络时间，tuya_sdk会进行时区和夏令时的校准；

#### uni_time_check_time_sync

1. 可检测设备夏令时和时区是否校准成功。

```c
/***********************************************************
*  Function: uni_time_check_time_sync
*  Desc:     check tuya-sdk inside time is synced with tuya-cloud
*  Return:   OPRT_OK: success  Other: fail
***********************************************************/
__UNI_TIME_EXT \
OPERATE_RET uni_time_check_time_sync(VOID);
```

#### uni_local_time_get
```c
/***********************************************************
*  Function: uni_local_time_get
*  Desc:     change the current time to tm_s,including the
*            timezone and the summer time zone info
*  Output:   tm
*  Return:   OPRT_OK: success  Other: fail
***********************************************************/
__UNI_TIME_EXT \
OPERATE_RET uni_local_time_get(OUT POSIX_TM_S *tm);
```

**代码参考**
```c
    POSIX_TM_S local_time;
    if(uni_time_check_time_sync() == OPRT_OK){
        op_ret = uni_local_time_get(&local_time);
        if(op_ret != OPRT_OK) {
            PR_ERR("uni_local_time_get op_ret:%d", op_ret);
        }
        PR_DEBUG("Current time:%d-%d-%d %d %d:%d:%d", \
                                        local_time.tm_year + 1900,\
                                        local_time.tm_mon + 1, \
                                        local_time.tm_mday, \
                                        local_time.tm_wday, \
                                        local_time.tm_hour, \
                                        local_time.tm_min, \
                                        local_time.tm_sec);
    }
```

### uni_time_get_posix

1. 用于获取系统时间戳

```c
/***********************************************************
*  Function: uni_time_get_posix
*  Desc:     get tuya-sdk inside time, time_t format
*  Return:   time_t
***********************************************************/
__UNI_TIME_EXT \
TIME_T uni_time_get_posix(VOID);
```

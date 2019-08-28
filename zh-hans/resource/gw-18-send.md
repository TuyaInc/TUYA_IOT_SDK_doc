### 状态上报

#### dp数据上报(obj类型)

![dp](images/dp.png)

```c
    TY_OBJ_DP_S dp_msg[3];
    dp_msg[0].dpid = ?; // 如上截图，去对应产品的功能点里获取
    dp_msg[0].type = PROP_VALUE;
    dp_msg[0].value.dp_value = ？; 
    dp_msg[0].time_stamp = 0; // 采用当前时间
    // 工作状态，如上图
    dp_msg[1].dpid = 3;
    dp_msg[1].type = PROP_ENUM;
    dp_msg[1].value.dp_enum = 1 ; // 制热
    dp_msg[1].time_stamp = 0;

    dp_msg[2].dpid = ？;
    dp_msg[2].type = PROP_STR;
    dp_msg[2].value.dp_str = "Tuya Smart"; // 最大支持长度 ？ 
    dp_msg[2].time_stamp = 0;

    op_ret =  dev_report_dp_json_async(cid,dp_msg,sizeof(dp_msg)/sizeof(TY_OBJ_DP_S));
    if (op_ret != OPRT_OK){
        PR_ERR("dev_report_dp_json_async op_ret:%d\n", op_ret);
        return op_ret;
    }
    PR_NOTICE("dev_report_dp_json_async success");
```

#### dev_report_dp_json_async
```c
/***********************************************************
*  Function: dev_report_dp_json_async
*  Desc:     report dp info a-synced.
*  Input:    devid: if sub-device, then devid = sub-device_id
*                   if gateway/soc/mcu, then devid = NULL
*  Input:    dp_data: dp array header
*  Input:    cnt    : dp array count
*  Return:   OPRT_OK: success  Other: fail
***********************************************************/
OPERATE_RET dev_report_dp_json_async(IN CONST CHAR_T *dev_id,IN CONST TY_OBJ_DP_S *dp_data,IN CONST UINT_T cnt);
```

#### dp数据上报(raw类型)

```c
/***********************************************************
*  Function: dev_report_dp_raw_sync
*  Desc:     report dp raw info synced.
*  Input:    devid: if sub-device, then devid = sub-device_id
*                   if gateway/soc/mcu, then devid = NULL
*  Input:    dpid: raw dp id
*  Input:    data: raw data
*  Input:    len : len of raw data
*  Input:    timeout: function blocks until timeout seconds
*  Return:   OPRT_OK: success  Other: fail
***********************************************************/
#define dev_report_dp_raw_sync(dev_id, dpid, data, len, timeout) \
    dev_report_dp_raw_sync_extend(dev_id, dpid, data, len, timeout, TRUE)
OPERATE_RET dev_report_dp_raw_sync_extend(IN CONST CHAR_T *dev_id,IN CONST BYTE_T dpid,\
                                                      IN CONST BYTE_T *data,IN CONST UINT_T len,\
                                                      IN CONST UINT_T timeout, IN CONST BOOL_T enable_auto_retrans);
```

```c
    BYTE_T dpid = ?; 
    BYTE_T data[3] = {0xF1,0xF2,0xF3};
    OPERATE_RET op_ret = dev_report_dp_raw_sync(cid,dpid,data,sizeof(data),5);
    if(OPRT_OK != op_ret) {
        PR_ERR("dev_report_dp_json_async op_ret:%d",op_ret);
    }
```
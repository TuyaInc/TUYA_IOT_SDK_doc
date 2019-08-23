## 流服务

利用 mqtt 满足一些小数据的流式传输服务,采用增量传输数据的方法,将数据挤牙膏式的发往云端以及 app,含起始通知、结束通知服务。

协议通信可靠性保障依赖于 mqtt qos=1 机制保障,端点数据发送均采用 qos=1 模式。

### 接口说明

#### tuya_iot_media_data_report

1. 为同步上报接口，PUBLISH(Qos1,Msg);

```c
/***********************************************************
*  Function: tuya_iot_media_data_report
*  Input: dt_body->media data
*         timeout->need report time
*  Output: none
*  Return: OPERATE_RET
***********************************************************/
OPERATE_RET tuya_iot_media_data_report(IN CONST FLOW_BODY_ST *dt_body,IN CONST UINT_T timeout);
```

**代码参考**

```c
    OPERATE_RET op_ret;
    unsigned char data_buf[] = {"iot.tuya.com"}; // 示例数据
    FLOW_BODY_ST*flow_data=(FLOW_BODY_ST*)malloc(sizeof(FLOW_BODY_ST)+sizeof(data_buf));
    if(flow_data == NULL){
        PR_ERR("malloc flow_data");
        return OPRT_MALLOC_FAILED;
    }
    memset(flow_data, 0, sizeof(FLOW_BODY_ST)+sizeof(data_buf));

    flow_data->id = map_id;
    flow_data->posix = uni_time_get_posix();
    flow_data->step = TS_TRANSFER;
    flow_data->offset = 0;
    flow_data->len = sizeof(data_buf);
    memcpy((unsigned char *)flow_data->data, data_buf, flow_data->len);
    op_ret = tuya_iot_media_data_report(flow_data, 5);
    if (op_ret != OPRT_OK){
        PR_ERR("tuya_iot_media_data_report->TS_TRANSFER is error %d\n", op_ret);
    }
    free(flow_data);
    return op_ret;
```
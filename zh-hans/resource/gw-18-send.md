### 状态上报

- [接口说明参考](18-send.md)

### 代码参考

如下为上报zigbee门磁子设备的功能点
![16-1](images/16-1.png)

- 先获取此子设备配网时的dev_id, [即tuya_iot_gw_bind_dev_attr接口的传入的id参数](gw-08-sub_active.md#tuyaiotgwbinddevattr)

#### 上报门磁状态(布尔型数据上报)

```c
    TY_OBJ_DP_S doorcontact_state;
    doorcontact_state.dpid = 1; 
    doorcontact_state.type = PROP_BOOL;
    doorcontact_state.value.dp_bool = TRUE; 
    doorcontact_state.time_stamp = 0; // 采用当前时间

    op_ret =  dev_report_dp_json_async(dev_id,&doorcontact_state,sizeof(doorcontact_state)/sizeof(TY_OBJ_DP_S));
    if (op_ret != OPRT_OK){
        PR_ERR("dev_report_dp_json_async op_ret:%d\n", op_ret);
        return op_ret;
    }
    PR_NOTICE("dev_report_dp_json_async success");
```

#### 上报电池电量(数值型数据上报)

```c
    TY_OBJ_DP_S battery_percentage;
    battery_percentage.dpid = 2; 
    battery_percentage.type = PROP_VALUE;
    // 注意取值要在平台定义的数值范围内，如上图数值范围为0-100
    battery_percentage.value.dp_value = 80; 
    battery_percentage.time_stamp = 0; // 采用当前时间

    op_ret =  dev_report_dp_json_async(dev_id,&battery_percentage,sizeof(battery_percentage)/sizeof(TY_OBJ_DP_S));
    if (op_ret != OPRT_OK){
        PR_ERR("dev_report_dp_json_async op_ret:%d\n", op_ret);
        return op_ret;
    }
    PR_NOTICE("dev_report_dp_json_async success");
```

#### 上报电池电量状态(枚举型数据上报)

- 枚举值low, middle, high
> [!注意事项]
> 枚举值从0开始，即low状态上报0，middle状态上报1.....

```c
typedef enum {
    STATUS_low = 0,
    STATUS_middle, 
    STATUS_high,
}STATUS_TYPE_E;

    TY_OBJ_DP_S battery_state;
    battery_state.dpid = 3; 
    battery_state.type = PROP_ENUM;
    battery_state.value.dp_enum = STATUS_middle; 
    battery_state.time_stamp = 0; // 采用当前时间

    op_ret =  dev_report_dp_json_async(dev_id,&battery_state,sizeof(battery_state)/sizeof(TY_OBJ_DP_S));
    if (op_ret != OPRT_OK){
        PR_ERR("dev_report_dp_json_async op_ret:%d\n", op_ret);
        return op_ret;
    }
    PR_NOTICE("dev_report_dp_json_async success");
```
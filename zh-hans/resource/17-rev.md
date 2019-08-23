## 设备控制

### 设备指令下发链路图
```uml
@startuml
title Device control
participant TuyaApp
participant TuyaCloud
participant tuya_sdk
participant Device

Note over tuya_sdk:255.255.255.255:6667
Note over TuyaApp:用户已打开
TuyaApp->tuya_sdk:局域网内搜索到设备，建立tcp连接
tuya_sdk-->TuyaApp:ACK
TuyaApp->tuya_sdk:直接走局域网下发
tuya_sdk->Device:__soc_dev_obj_dp_cmd_cb(LAN)
Note over TuyaApp:设备局域网已断开
TuyaApp->TuyaCloud:PUBLISH(Qos1,Msg)
TuyaCloud-->TuyaCloud:Store(Msg)
TuyaCloud->tuya_sdk:PUBLISH(Qos1,Msg)
TuyaCloud-->TuyaApp:PUBACK
tuya_sdk-->TuyaCloud:PUBACK
tuya_sdk->Device:__soc_dev_obj_dp_cmd_cb(MQ)
TuyaCloud-->TuyaCloud:Delete(Msg)
@enduml
```

**说明**
1. 如上图为TuyaApp处于打开情况下，

### 设备指令下发回调

#### dp下发数据结构说明(obj类型)
<table>
    <tr>
        <td>数据结构</td> 
        <td>成员</td> 
        <td>说明</td>
   </tr>
    <tr>
        <td rowspan="6">TY_RECV_OBJ_DP_S</td>    
        <td >cmd_tp</td> 
        <td >指令类型<br/>
            [√] DP_CMD_LAN, 局域网控制<br/>
            [√] DP_CMD_MQ,  云端指令<br/>
            [√] DP_CMD_TIMER,   设备定时<br/>
            [√] DP_CMD_SCENE_LINKAGE, 场景联动<br/>
            [√] DP_CMD_RELIABLE_TRANSFER, 可靠传输
        </td>
    </tr>
    <tr>
        <td >dtt_tp</td>
        <td > / </td>  
    </tr>
    <tr>
        <td >cid</td>
        <td >
            cid = NULL, 为网关或soc设备dp下发;<br/>
            cid != NULL,为子设备dp,cid为子设备id;<br/>
        </td> 
    </tr>
    <tr>
        <td >mb_id</td>
        <td >群组ID</td> 
    </tr>
    <tr>
        <td >dps_cnt</td>
        <td >下发的dp值数量</td> 
    </tr>
    <tr>
        <td >dps</td>
        <td >dp值缓存首地址，为TY_OBJ_DP_S数据结构</td> 
    </tr>
</table>

dp值数据结构说明 见头文件 tuya_cloud_com_defs.h TY_OBJ_DP_S数据结构

#### __soc_dev_obj_dp_cmd_cb

```c
/***********************************************************
 * @Function:__soc_dev_obj_dp_cmd_cb
 * @Desc:   dp指令下发入口(obj类型)
 * @Param:  dp,    dp下发数据结构 缓存首地址
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
VOID __soc_dev_obj_dp_cmd_cb(IN CONST TY_RECV_OBJ_DP_S *dp)
{
    PR_DEBUG("SOC Rev DP Obj Cmd t1:%d t2:%d CNT:%u", dp->cmd_tp, dp->dtt_tp, dp->dps_cnt);
    if(dp->cid != NULL) PR_ERR("soc not have cid.%s", dp->cid);

    // 用户处理完成之后需要主动上报最新状态，这里简单起见，直接返回收到的数据，认为处理全部成功。
    OPERATE_RET op_ret = dev_report_dp_json_async(dp->cid,dp->dps,dp->dps_cnt);
    if(OPRT_OK != op_ret) {
        PR_ERR("dev_report_dp_json_async op_ret:%d",op_ret);
    }
}

```

#### dp下发数据结构说明(raw类型)
<table>
    <tr>
        <td>数据结构</td> 
        <td>成员</td> 
        <td>说明</td>
   </tr>
    <tr>
        <td rowspan="7">TY_RECV_RAW_DP_S</td>    
        <td >cmd_tp</td> 
        <td >指令类型<br/>
            [√] DP_CMD_LAN, 局域网控制<br/>
            [√] DP_CMD_MQ,  云端指令<br/>
            [√] DP_CMD_TIMER,   设备定时<br/>
            [√] DP_CMD_SCENE_LINKAGE, 场景联动<br/>
            [√] DP_CMD_RELIABLE_TRANSFER,
        </td>
    </tr>
    <tr>
        <td >dtt_tp</td>
        <td > / </td>  
    </tr>
    <tr>
        <td >cid</td>
        <td >
            cid = NULL, 为网关或soc设备dp下发;<br/>
            cid != NULL,为子设备dp,cid为子设备id;<br/>
        </td> 
    </tr>
    <tr>
        <td >dp_id</td>
        <td >功能点编号</td> 
    </tr>
    <tr>
        <td >mb_id</td>
        <td >群组ID</td> 
    </tr>
    <tr>
        <td >len</td>
        <td >下发的raw数据长度</td> 
    </tr>
    <tr>
        <td >data</td>
        <td >raw数据缓存首地址</td> 
    </tr>
</table>

#### __soc_dev_raw_dp_cmd_cb
```c
/***********************************************************
 * @Function:__gw_dev_raw_dp_cmd_cb
 * @Desc:   dp指令下发入口(raw类型)
 * @Param:  dp,    dp下发数据结构 缓存首地址
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
VOID __soc_dev_raw_dp_cmd_cb(IN CONST TY_RECV_RAW_DP_S *dp)
{
    PR_NOTICE("SOC Rev DP Raw Cmd t1:%d t2:%d dpid:%d len:%u", dp->cmd_tp, dp->dtt_tp, dp->dpid, dp->len);
    if(dp->cid != NULL) PR_ERR("soc not have cid.%s", dp->cid);

    //User TODO

    //用户处理完成之后需要主动上报最新状态，这里简单起见，直接返回收到的数据，认为处理全部成功。
    OPERATE_RET op_ret = dev_report_dp_raw_sync(dp->cid,dp->dpid,dp->data,dp->len,0);
    if(OPRT_OK != op_ret) {
        PR_ERR("dev_report_dp_json_async op_ret:%d",op_ret);
    }
}
```
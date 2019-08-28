# 涂鸦sdk常见错误码

便于开发调试过程中定位问题。

### -944
OPRT_DP_ID_NOT_FOUND                (-944)

上报功能点时，对应的DPID不在该产品下
### -916
OPRT_GW_MQ_OFFLILNE                 (-916)

设备与涂鸦云mqtt服务器长连接断开，可能原因：设备网络波动，网络心跳包超时
### -926
OPRT_MQ_PUBLISH_TIMEOUT             (-926)

设备上报dp数据状态，等待服务器puback超时。

### -945
OPRT_DP_TP_NOT_MATCH                (-945)

上报功能点属性不匹配，比如平台上定义为枚举型dp,上报时类型参数传错
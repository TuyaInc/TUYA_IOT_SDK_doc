## 初始化

sdk功能正常运行的基本条件是按init函数调用顺序完成初始化操作；

### 设备认证
设备的身份认证采用一机一密的方式，在设备上烧写设备的唯一的UUID、AUTHKEY，这种方式要求对设备的产线工具进行一定的修改，需要对每个设备烧写不同的UUID和AUTHKEY；
UUID和AUTHKEY成对出现，每一台设备都必须有自己uuid & authkey，且唯一，请向涂鸦项目经理申请几组用于调试。

### 函数调用流程

使用sdk其他接口功能前，要现在线程中调用如下接口完成tuya_sdk初始化过程；

```uml
@startuml
title tuya_sdk初始化调用流程
start
:tuya_iot_init;
:AddOutputTerm(可选);
:SetLogManageAttr(可选);
:tuya_iot_set_gw_prod_info;
:tuya_iot_wf_soc_dev_init;
:tuya_iot_reg_get_nw_stat_cb;
stop
@enduml
```
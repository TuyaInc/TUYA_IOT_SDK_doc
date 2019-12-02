# 通用联网模块 SDK

## 版本记录

| 版本  | 编写/修订说明    | 修订人 | 修订日期   |
| ----- | ---------------- | ------ | ---------- |
| 1.0.0 | 创建             | 赵春生 | 2019/11/20 |
| 1.0.1 | 根据内部讨论优化 | 赵春生 | 2019/11/27 |
| 1.0.2 | 优化部分措辞说明 | 赵春生 | 2019/11/29 |

## 1 概述
联网模块SDK：借助设备的联网能力，直接与涂鸦云、涂鸦app建立通信链路并进行涂鸦标准数据交互的一个软件中间件；比如无线网络、有线网络、gprs、3g/4g等。

**功能描述**

- 提供符合涂鸦标准数据规范的上下行通道
- 提供数据过滤服务：数据校验、规则上报、上报频率控制等
- 提供涂鸦标准化服务，例如本地定时、本地联动等功能
- 通信传输过程中的数据安全服务

## 2 产品形态

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/13877920-57c2-42bf-9eb0-a2c71fdf834d.png)

### 2.1 联网模组+MCU类产品

利用联网模块+外部mcu做成的单品，功能相对复杂，模块资源不足以完成产品整体功能的开发、需要借助外部mcu实现。此时联网模块主要作为外部mcu与涂鸦云、涂鸦app之间的桥梁

联网模块同时提供模块自身以及客户mcu的固件升级能力。

### 2.2 SOC类产品

仅利用联网模块做成的单品，功能相对简单，所有的产品功能均在模块内实现。例如：智能插座、智能led灯等

### 2.3 网关类产品

利用联网模块+不同的协议控制器将产品做成实体网关产品，网关向下接入各类不同通信协议的产品，比如：zigbee、rf类、ble、有线485等。比如：智能家居中常见的控制中心、智慧主机等。

网关提供联网模块、协议控制器、终端设备的固件升级能力。

### 2.4 网关+设备类产品

利用联网模块+不同的协议控制器将产品做成带设备功能的网关产品，网关向下接入各类不同通信协议的产品，比如：zigbee、rf类、ble、有线485等，同时自身又是一个产品。自身的产品功能由联网模块实现。

网关提供联网模块、协议控制器、终端设备的固件升级能力

### 2.5 设备配网方式

设备完整的配网过程：设备要先连通外网，然后sdk去涂鸦云激活，完成整个设备的配网过程。

对于设备连通外网的过程，不同形态的设备方式如下：

1. 没有人机交互界面的wifi设备，使用前需要app通过smartconfig、ap等技术进行配网，当设备接收连网信息，连上路由器之后才可以接通外网。
2. 有人机交互界面的wifi设备、有线网络产品、gprs/4G等通过运营商方式已经具备联通外网能力的设备，无需app参与配置。

## 3 涂鸦云联网模块sdk适用范围

### 3.1 必备条件
- 支持linux 或 android
- 支持tcp/ip协议栈
- 标准linux系统，支持文件系统

### 3.2 说明

满足"3.1"，涂鸦云联网模块sdk可适用于不同芯片、不同os平台的应用场景。客户可根据产品类别、产品形态按需调用sdk提供的接口实现产品功能。

### 3.2 资源占用
- Flash，sdk代码占用2MB左右
- RAM，sdk运行后内存占用5MB，用户要根据开发产品的功能复杂度增加内存

## 4 编译说明

### 4.1 linux平台

涂鸦根据客户toolchain编译成的相关的库，附带开发手册以及相关demo等提供给客户。

### 4.2 android 平台

涂鸦根据客户提供的 API版本号，使用NDK产生工具链，并编译成的相关的库，附带开发手册以及相关demo等提供给客户。


## 5 SDK初始化

### 5.1 设备认证
- 设备认证：
    - 设备的身份认证采用一机一密的方式，在设备上烧写设备的唯一的UUID、AUTHKEY，这种方式要求对设备的产线工具进行一定的修改，需要对每个设备烧写不同的UUID和AUTHKEY；
    - UUID和AUTHKEY成对出现，每一台设备都必须有自己uuid & authkey，且唯一，请向涂鸦项目经理申请几组用于调试；
    - 设备认证消息通过接口[tuya_iot_set_gw_prod_info](#tuya_iot_set_gw_prod_info)设置

在demo 中 PID，uuid & authkey仅用作测试使用，不能用于实际产品，会导致后续产品不可用。PID需要用户自行从涂鸦开发者平台申请。每一台设备都必须有自己uuid &authkey，且唯一。

### 5.2 网关类产品
![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/443b4d23-ace1-49d4-8567-ff357e5c75c1.png)

### 5.3 单品
![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/d8c89dd9-aaa5-4449-8061-95a1b4a36a01.png)

### 5.4 初始化接口说明

#### 5.4.1 tuya\_iot\_init

**函数原型**

OPERATE\_RET tuya\_iot\_init(IN CONST CHAR\_T \*fs\_storge\_path)

**功能说明**

用于tuya iot 系统的初始化，必须最先调用

**参数说明**

| **参数名称**     | **如何理解**               | **参数类型** | **是否必选** | **如何设置**                    |
| ---------------- | -------------------------- | ------------ | ------------ | ------------------------------- |
| fs\_storge\_path | 为sdk 分配可读写的文件分区 | CHAR\_T      | 是           | **路径长度不能大于110个字节。** |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 成功           |
| 错误码     | 失败返回错误码 |

#### 5.4.2 tuya\_iot\_get\_sdk\_info

**函数原型**

CHAR\_T \*tuya\_iot\_get\_sdk\_info(VOID);

**功能说明**

获取tuya iot sdk版本信息。

**参数说明**

| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------|--------------|--------------|--------------|
| VOID         |              |              |              |              |

**返回值**

| **返回值**     | **说明**                                                         |
|----------------|------------------------------------------------------------------|
| SDK 信息字符串 | SDK 信息包含 SDK的编译时间，平台，以及版本号，使能的功能属性等。 |

#### 5.4.3 tuya\_iot\_sys\_mag\_hb\_init

**函数原型**

OPERATE\_RET tuya\_iot\_sys\_mag\_hb\_init(IN CONST
DEV\_HEARTBEAT\_SEND\_CB hb\_send\_cb);

**功能说明**

启动子设备心跳管理能力。

**参数说明**

| **参数名称** | **如何理解**                                                                                                                                   | **参数类型**          | **是否必选** | **如何设置** |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|--------------|--------------|
| hb\_send\_cb | 网关每隔3秒检查所有的子设备。如果子设备在心跳包超时内，子设备没有发送心跳给网关，则网关会设置子设备为离线，通过hb\_send\_cb 通知用户至少三次。 | hb\_send\_cb 回调函数 | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### 5.4.4  wifi配置类设备接口，详见"tuya\_iot\_wifi\_api.h"
----------------------------------------------------

#####  tuya\_iot\_set\_wf\_gw\_prod\_info

**函数原型**

OPERATE\_RET tuya\_iot\_set\_wf\_gw\_prod\_info(IN CONST
WF\_GW\_PROD\_INFO\_S \*wf\_prod\_info);

**功能说明**

设置配置类wifi设备的授权信息，授权信息需通过涂鸦获取，否则设备无法正常使用。

**参数说明**

| **参数名称**   | **如何理解**                                                          | **参数类型**             | **是否必选** | **如何设置**                   |
|----------------|-----------------------------------------------------------------------|--------------------------|--------------|--------------------------------|
| wf\_prod\_info | 该结构体包含设备信息：1）uuid 以及 authkey 2) wifi 的 ssid 以及密码。 | WF\_GW\_PROD\_INFO\_S \* | 是           | 开发者需要把该信息传递给 sdk。 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_wf\_mcu\_dev\_init

**函数原型**

OPERATE\_RET tuya\_iot\_wf\_mcu\_dev\_init(IN CONST
GW\_WF\_CFG\_MTHD\_SEL cfg, IN CONST GW\_WF\_START\_MODE start\_mode, IN
CONST TY\_IOT\_CBS\_S \*cbs, IN CONST CHAR\_T \*p\_firmware\_key, IN
CONST CHAR\_T \*product\_key,IN CONST CHAR\_T \*wf\_sw\_ver,IN CONST
CHAR\_T \*mcu\_sw\_ver);

**功能说明**

联网模块+mcu 设备初始化接口。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>cfg</td>
<td><p>wifi设备的工作方式。工作方式：</p>
<p>1）非低功耗模式。</p>
<p>2）低功耗模式。</p>
<p>3）特别的低功耗模式。</p></td>
<td>GW_WF_CFG_MTHD_SEL</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>start_mode</td>
<td><p>Wifi 配网模式：</p>
<p>1）仅工作在ap 配置模式。</p>
<p>2）仅smart config 模式式。</p>
<p>3）ap 以及smart config 模式，默认ap 模式。</p>
<p>4）ap 以及 smart config 模式。默认 smart config模式。</p>
<p>根据实际需要选择模式。</p></td>
<td>GW_WF_START_MODE</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>cbs</td>
<td>Wifi sdk 用户回调函数</td>
<td>TY_IOT_CBS_S *</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>p_firmware_key</td>
<td>固件key。固件上传至涂鸦平台，都会有固件key。</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>product_key</td>
<td>涂鸦平台创建产品时获取</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>wf_sw_ver</td>
<td>Wifi 软件版本号</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>mcu_sw_ver</td>
<td>Mcu 固件版本号</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_wf\_soc\_dev\_init

**函数原型**

OPERATE\_RET tuya\_iot\_wf\_soc\_dev\_init(IN CONST
GW\_WF\_CFG\_MTHD\_SEL cfg, IN CONST GW\_WF\_START\_MODE start\_mode, IN
CONST TY\_IOT\_CBS\_S \*cbs, IN CONST CHAR\_T \*product\_key,IN CONST
CHAR\_T \*wf\_sw\_ver);

**功能说明**

wifi soc 设备初始化接口。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>cfg</td>
<td><p>wifi设备的工作方式。工作方式：</p>
<p>1）非低功耗模式。</p>
<p>2）低功耗模式。</p>
<p>3）特殊低功耗模式。</p></td>
<td>GW_WF_CFG_MTHD_SEL</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>start_mode</td>
<td><p>Wifi 配网模式：</p>
<p>1）仅工作在ap 配置模式。</p>
<p>2）仅smart config 模式式。</p>
<p>3）ap 以及smart config 模式，默认ap 模式。</p>
<p>4）ap 以及 smart config 模式。默认 smart config模式。</p>
<p>根据实际需要选择模式。</p></td>
<td>GW_WF_START_MODE</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>cbs</td>
<td>Wifi sdk 用户回调函数</td>
<td>TY_IOT_CBS_S *</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>product_key</td>
<td>涂鸦平台创建产品时获取</td>
<td>字符串</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>wf_sw_ver</td>
<td>软件版本号</td>
<td>字符串</td>
<td></td>
<td>xx.xx.xx</td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_wf\_gw\_init

**函数原型**

OPERATE\_RET tuya\_iot\_wf\_gw\_init(IN CONST GW\_WF\_CFG\_MTHD\_SEL
cfg, IN CONST GW\_WF\_START\_MODE start\_mode, IN CONST TY\_IOT\_CBS\_S
\*cbs, IN CONST TY\_IOT\_GW\_CBS\_S \*gw\_cbs, IN CONST CHAR\_T
\*product\_key, IN CONST CHAR\_T \*wf\_sw\_ver, IN CONST
GW\_ATTACH\_ATTR\_T \*attr, IN CONST UINT\_T attr\_num);

**功能说明**

wifi 网关初始化接口

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>cfg</td>
<td><p>wifi设备的工作方式。工作方式：</p>
<p>1）非低功耗模式。</p>
<p>2）低功耗模式。</p>
<p>3）特殊低功耗模式。</p></td>
<td>GW_WF_CFG_MTHD_SEL</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>start_mode</td>
<td><p>Wifi 配网模式：</p>
<p>1）仅工作在ap 配置模式。</p>
<p>2）仅smart config 模式式。</p>
<p>3）ap 以及smart config 模式，默认ap 模式。</p>
<p>4）ap 以及 smart config 模式。默认 smart config模式。</p></td>
<td>GW_WF_START_MODE</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>cbs</td>
<td>涂鸦wifi sdk 用户回调</td>
<td>TY_IOT_CBS_S *</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>gw_cbs</td>
<td>涂鸦网关用户回调</td>
<td>TY_IOT_GW_CBS_S *</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>product_key</td>
<td>涂鸦平台创建产时获取</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>wf_sw_ver</td>
<td>软件版本号</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>attr</td>
<td>网关属性数组</td>
<td>GW_ATTACH_ATTR_T *</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>attr_num</td>
<td>网关属性长度</td>
<td>UINT_T</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_wf\_gw\_dev\_init

**函数原型**

OPERATE\_RET tuya\_iot\_wf\_gw\_dev\_init(IN CONST
GW\_WF\_CFG\_MTHD\_SEL cfg, IN CONST GW\_WF\_START\_MODE start\_mode, IN
CONST TY\_IOT\_CBS\_S \*cbs, IN CONST TY\_IOT\_GW\_CBS\_S \*gw\_cbs, IN
CONST CHAR\_T \*product\_key, IN CONST CHAR\_T \*wf\_sw\_ver, IN CONST
GW\_ATTACH\_ATTR\_T \*attr, IN CONST UINT\_T attr\_num);

**功能说明**

wifi 网关加设备初始化接口，相比与tuya\_iot\_wf\_gw\_init，该函数使得网关具有设备的属性，可以设置功能点等。

**参数说明**

参考 tuya\_iot\_wf\_gw\_init 接口说明

**返回值**

参考 tuya\_iot\_wf\_gw\_init 接口说明


##### tuya\_iot\_reg\_get\_wf\_nw\_stat\_cb

**函数原型**

OPERATE\_RET tuya\_iot\_reg\_get\_wf\_nw\_stat\_cb(IN CONST
GET\_NW\_STAT\_CB nw\_stat\_cb);

**功能说明**

获取wifi状态接口。

**参数说明**

| **参数名称** | **如何理解**                                     | **参数类型** | **是否必选** | **如何设置** |
| ------------ | ------------------------------------------------ | ------------ | ------------ | ------------ |
| nw\_stat\_cb | 涂鸦sdk 网络检测回调，回调函数的参数为网络状态。 | nw\_stat\_cb | 是           | 详见demo     |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


#### 5.4.5 非wifi配置类设备接口，详见"tuya\_iot\_base\_api.h"

##### tuya\_iot\_set\_gw\_prod\_info

**函数原型**

OPERATE\_RET tuya\_iot\_set\_gw\_prod\_info(IN CONST GW\_PROD\_INFO\_S
\*prod\_info);

**功能说明**

设置非wifi配置类联网模块的授权信息，授权信息需通过涂鸦获取，否则设备无法正常使用。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>prod_info</td>
<td>用户需要传递设备的uuid以及 authkey 到该结构体中。</td>
<td>CONST GW_PROD_INFO_S</td>
<td>是</td>
<td><p>UUID 的长度不应大于16字节，authkey 长度不应大于32字节。</p>
<p>该结构定义详见tuya_cloud_base_defs.h</p></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_mcu\_dev\_init

**函数原型**

OPERATE\_RET tuya\_iot\_mcu\_dev\_init(IN CONST TY\_IOT\_CBS\_S \*cbs,IN
CONST CHAR\_T \*product\_key, IN CONST CHAR\_T \*p\_firmware\_key, IN
CONST CHAR\_T \*sw\_ver,IN CONST CHAR\_T \*mcu\_sw\_ver);

**功能说明**

联网模块+mcu 设备初始化接口。

**参数说明**

| **参数名称**     | **如何理解**                                       | **参数类型**       | **是否必选** | **如何设置**                                          |
|------------------|----------------------------------------------------|--------------------|--------------|-------------------------------------------------------|
| cbs              | Cbs 回调函数数组，用户在回调函数中实现自己的功能。 | TY\_IOT\_CBS\_S \* | 是           | 参看例子的中的使用方法。                              |
| product\_key     | 在平台创建产品时产品的产品ID                       | 字符串             | 是           | 注意：长度不超过16个字节。                            |
| p\_firmware\_key | 上传至涂鸦平台mcu固件key                           | 字符串             | 是           | 注意：长度不超过16个字节。                            |
| sw\_ver          | 软件版本号                                         | 字符串             | 是           | 网关固件版本号。合法的格式：xx.xx.xx （0\<=x \<= 9）  |
| mcu\_sw\_ver     | mcu 软件版本号                                     |                    |              | 设备固件版本号。 合法的格式：xx.xx.xx （0\<=x \<= 9） |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_io\_soc\_init

**函数原型**

OPERATE\_RET tuya\_iot\_soc\_init(IN CONST TY\_IOT\_CBS\_S \*cbs, IN
CONST CHAR\_T \*product\_key, IN CONST CHAR\_T \*sw\_ver);

**功能说明**

联网模块soc 设备初始化接口。

**参数说明**

| **参数名称** | **如何理解**                                   | **参数类型**       | **是否必选** | **如何设置**                                          |
|--------------|------------------------------------------------|--------------------|--------------|-------------------------------------------------------|
| cbs          | 回调函数数组，用户在回调函数中实现自己的功能。 | TY\_IOT\_CBS\_S \* | 是           | 参看例子的中的使用方法。                              |
| product\_key | 从涂鸦开放平台获取的product key                | 字符串             | 是           | 注意：长度不超过16个字节。                            |
| sw\_ver:     | 软件版本号                                     | 字符串             | 是           | 设备固件版本号。 合法的格式：xx.xx.xx （0\<=x \<= 9） |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_gw\_init

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_init(IN CONST TY\_IOT\_CBS\_S \*cbs,IN CONST
TY\_IOT\_GW\_CBS\_S \*gw\_cbs, IN CONST CHAR\_T \*product\_key,IN CONST
CHAR\_T \*sw\_ver, IN CONST GW\_ATTACH\_ATTR\_T \*attr, IN CONST UINT\_T
attr\_num);

**功能说明**

联网模块网关初始化接口。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>cbs</td>
<td>回调函数数组，用户在回调函数中实现自己的功能</td>
<td>TY_IOT_CBS_S *</td>
<td>是</td>
<td>参看例子的中的使用方法。</td>
</tr>
<tr class="even">
<td>gw_cbs</td>
<td>子设备管理回调函数</td>
<td>TY_IOT_GW_CBS_S *</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>product_key</td>
<td>从涂鸦开放平台获取的product key</td>
<td>字符串</td>
<td>是</td>
<td>注意：长度不超过16个字节。</td>
</tr>
<tr class="even">
<td>sw_ver</td>
<td>网关固件版本号</td>
<td>字符串</td>
<td>是</td>
<td>网关固件版本号。合法的格式：xx.xx.xx （0&lt;=x &lt;= 9）</td>
</tr>
<tr class="odd">
<td>attr</td>
<td>网关参数数组</td>
<td>GW_ATTACH_ATTR_T *</td>
<td>是</td>
<td><p>例如：GW_ATTACH_ATTR_T attr[] = {</p>
<p>{GP_DEV_ZB,"1.0.0"},</p>
<p>};</p></td>
</tr>
<tr class="even">
<td>attr_num</td>
<td>参数数组长度</td>
<td>UINT_T</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_gw\_dev\_init

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_dev\_init(IN CONST TY\_IOT\_CBS\_S \*cbs, IN
CONST TY\_IOT\_GW\_CBS\_S \*gw\_cbs, IN CONST CHAR\_T \*product\_key, IN
CONST CHAR\_T \*sw\_ver,IN CONST GW\_ATTACH\_ATTR\_T \*attr,IN CONST
UINT\_T attr\_num);

**功能说明**

联网模块网关加设备初始化接口，相比与tuya\_iot\_gw\_init，该函数使得网关具有设备的属性，可以设置功能点等。

**参数说明**

参考 tuya\_iot\_gw\_init 接口说明

**返回值**

参考 tuya\_iot\_gw\_init 接口说明

##### tuya\_iot\_reg\_get\_nw\_stat\_cb

**函数原型**

OPERATE\_RET tuya\_iot\_reg\_get\_nw\_stat\_cb (IN CONST
GET\_NW\_STAT\_CB nw\_stat\_cb);

**功能说明**

注册联网网络状态获取函数，在注册的回调函数中，用户可以根据获取当前的网络状态信息。

**参数说明**

| **参数名称** | **如何理解**                               | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------------------------------------|--------------|--------------|--------------|
| nw\_stat\_cb | 网络改变回调函数，回调函数的参数为网络状态 | nw\_stat\_cb | 是           | 详见 demo。  |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### 5.4.6 有线加wifi 配置接口，详见 "tuya\_iot\_wired\_wifi\_api.h"

##### tuya\_iot\_set\_wired\_wifi\_gw\_prod\_info

**函数原型**

tuya\_iot\_set\_wired\_wifi\_gw\_prod\_info(IN CONST
WF\_GW\_PROD\_INFO\_S \*wf\_prod\_info);

参看 tuya\_iot\_set\_wf\_gw\_prod\_info
函数说明，只用方法以及参数与其一直。

##### tuya\_iot\_wired\_wifi\_gw\_dev\_init

**函数原型**

OPERATE\_RET tuya\_iot\_wired\_wifi\_gw\_dev\_init(IN CONST
IOT\_GW\_NET\_TYPE\_T net\_mode,

IN CONST GW\_WF\_CFG\_MTHD\_SEL cfg, IN CONST GW\_WF\_START\_MODE
start\_mode,

IN CONST TY\_IOT\_CBS\_S \*cbs, IN CONST TY\_IOT\_GW\_CBS\_S \*gw\_cbs,

IN CONST CHAR\_T \*product\_key, IN CONST CHAR\_T \*wf\_sw\_ver,

IN CONST GW\_ATTACH\_ATTR\_T \*attr, IN CONST UINT\_T attr\_num);

**功能说明**

有线 + wifi 网关 （网关同时作为设备）初始化接口。

**功能说明**

wifi 网关加设备初始化接口。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Net_name</td>
<td>支持的设备网络类型</td>
<td>IOT_GW_NET_TYPE_T</td>
<td>是</td>
<td>IOT_GW_NET_WIRED_WIFI</td>
</tr>
<tr class="even">
<td>cfg</td>
<td><p>wifi设备的工作方式。工作方式：</p>
<p>1）非低功耗模式。</p>
<p>2）低功耗模式。</p>
<p>3）特殊低功耗模式。</p></td>
<td>GW_WF_CFG_MTHD_SEL</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>start_mode</td>
<td><p>Wifi 配网模式：</p>
<p>1）仅工作在ap 配置模式。</p>
<p>2）仅smart config 模式式。</p>
<p>3）ap 以及smart config 模式，默认ap 模式。</p>
<p>4）ap 以及 smart config 模式。默认 smart config模式。</p></td>
<td>GW_WF_START_MODE</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>cbs</td>
<td>涂鸦wifi sdk 用户回调</td>
<td>TY_IOT_CBS_S *</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>gw_cbs</td>
<td>涂鸦网关用户回调</td>
<td>TY_IOT_GW_CBS_S *</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>product_key</td>
<td>涂鸦平台创建产时获取</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>wf_sw_ver</td>
<td>软件版本号</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>attr</td>
<td>网关属性数组</td>
<td>GW_ATTACH_ATTR_T *</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>attr_num</td>
<td>网关属性长度</td>
<td>UINT_T</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_reg\_get\_wired\_wifi\_nw\_stat\_cb

**函数原型**

OPERATE\_RET
tuya\_iot\_reg\_get\_wired\_wifi\_nw\_stat\_cb(nw\_stat\_cb,
wf\_nw\_stat\_cb);

**功能说明**

注册网络状态管理回调函数

**参数说明**

| **参数名称**     | **如何理解**     | **参数类型**          | **是否必选** | **如何设置** |
|------------------|------------------|-----------------------|--------------|--------------|
| nw\_stat\_cb     | 网关网络状态回调 | GW\_BASE\_NW\_STAT\_T | 是           | 详见 demo。  |
| wf\_nw\_stat\_cb | WIFI网络状态回调 | GW\_WIFI\_NW\_STAT\_E | 是           | 详见 demo。  |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_wired\_wifi\_gw\_unactive

**函数原型**

OPERATE\_RET tuya\_iot\_wired\_wifi\_gw\_unactive(VOID);

参看 tuya\_iot\_wf\_gw\_unactive 使用方法一直。

##### tuya\_iot\_gw\_wired\_wifi\_set\_net\_name

**函数原型**

VOID tuya\_iot\_gw\_wired\_wifi\_set\_net\_name(IN CONST CHAR\_T
\*wired\_net\_name, IN CONST CHAR\_T \*wifi\_net\_name);

**功能说明**

设置有线，以及wifi 网络接口名称。

**参数说明**

| **参数名称**     | **如何理解**      | **参数类型** | **是否必选** | **如何设置** |
|------------------|-------------------|--------------|--------------|--------------|
| wired\_net\_name | 有线网络接口名称  | CHAR\*       | 是           | 例如： eth0  |
| wifi\_net\_name  | WiFi 接口网络名称 | CHAR\*       | 是           | 例如：ath0   |

**返回值**

| **返回值** | **说明** |
| ---------- | -------- |
| VOID       |          |



## 6 设备配网

> 说明：tuya_sdk会开启多线程，进入配网模式生效必须重启整个进程。
>
> 因为涂鸦sdk兼顾的平台很多，无法兼顾所有的联网模块的硬件细节特性，因此对于sdk内部而言，抽象出了一套需要实现的联网设备相关的硬件接口，按产品形态分为两类：wifi配置类网络接口、非wif配置类网络接口，具体说明详见"demo"代码

### 6.1 有线设备配网

设备通过有线接入互联网,不用输入路由器的热点名称和密码，设备已连接外网；只需从APP端获取激活token，即可申请在涂鸦云激活设备。

#### 6.1.1 重置设备(进入待配网状态)

##### tuya\_iot\_gw\_unactive

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_unactive(VOID);

**功能说明**

重置网关设备，解除网关有APP的绑定关系，使得网关处于非激活(待配网)状态。

**参数说明**

|              |              |              |              |              |
|--------------|--------------|--------------|--------------|--------------|
| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### 6.1.2 配网流程交互图

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/0cf0c8a2-dad2-4f48-8a27-1182febd26b9.png)

- 实现6.1.2 ~6.1.4 接口；
- 如上图，本地重置设备后，需要重启sdk进程，才可进入配网模式。
- DEV_RESET_IFM_CB 参考常见问题说明

#### 6.1.3 需要用户实现的接口

##### hwl\_bnw\_get\_ip

**函数原型**

OPERATE\_RET hwl\_bnw\_get\_ip(OUT NW\_IP\_S \*ip);

**功能说明**

获取联网模块ip 信息。

**参数说明**

| **参数名称** | **如何理解**                                                            | **参数类型** | **是否必选** | **如何设置**                     |
|--------------|-------------------------------------------------------------------------|--------------|--------------|----------------------------------|
| ip           | IP 信息结构体。联网模块IP，以及当前网络的子网掩码，当前网络的网关地址。 | NW\_IP\_S \* | 是           | 用户需要填充IP 中的ip 地址信息。 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_bnw\_station\_conn

**函数原型**

BOOL\_T hwl\_bnw\_station\_conn(VOID);

**功能说明**

获取网络连接状态。

**参数说明**

VOID

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| TRUE       | 设备已经联网   |
| FALSE      | 设备断开网络。 |

##### hwl\_bnw\_get\_mac

**函数原型**

OPERATE\_RET hwl\_bnw\_get\_mac(OUT NW\_MAC\_S \*mac);

**功能说明**

获取设备网络接口的mac 地址。可选实现。

**参数说明**

| **参数名称** | **如何理解**                        | **参数类型**  | **是否必选** | **如何设置** |
|--------------|-------------------------------------|---------------|--------------|--------------|
| mac          | mac保存获取到的网络接口的mac 信息。 | NW\_MAC\_S \* | 是           |              |

**返回值**

| **返回值** | **说明** |
|------------|----------|
| OPRT\_OK   | 获取成功 |
| 错误码     | 获取失败 |

##### hwl\_bnw\_set\_mac

**函数原型**

OPERATE\_RET hwl\_bnw\_set\_mac(IN CONST NW\_MAC\_S \*mac);

**功能说明**

设定设备mac 地址，可选实现。

**参数说明**

| **参数名称** | **如何理解** | **参数类型**  | **是否必选** | **如何设置** |
|--------------|--------------|---------------|--------------|--------------|
| mac          |              | NW\_MAC\_S \* | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_bnw\_set\_station\_connect

**函数原型**

OPERATE\_RET hwl\_bnw\_set\_station\_connect(IN CONST CHAR\_T \*ssid,IN
CONST CHAR\_T \*passwd)

**功能说明**

当产品形态为有线+无线模式，需要用户自己实现 wifi
连接的函数，配网时app会将指定路由的ssid，password传下来。

**参数说明**

| **参数名称** | **如何理解**                        | **参数类型** | **是否必选** | **如何设置** |
|--------------|-------------------------------------|--------------|--------------|--------------|
| ssid         | app 会通知用户wifi ap 的ssid。      | CHAR\_T \*   | 是           |              |
| password     | app 会通知用户wifi ap 的 password。 | CHAR\_T \*   | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_bnw\_need\_wifi\_cfg

**函数原型**

BOOL\_T hwl\_bnw\_need\_wifi\_cfg(VOID);

**功能说明**

是否需要wifi 配置。

**参数说明**

|              |              |              |              |              |
|--------------|--------------|--------------|--------------|--------------|
| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |

**返回值**

| **返回值** | **说明**                                        |
|------------|-------------------------------------------------|
| TRUE       | 如果硬件有wifi 接口，用户想要连接wifi           |
| FALSE      | 如果硬件没有wifi接口，不想连接wifi 需返回false. |

##### hwl\_bnw\_station\_get\_conn\_ap\_rssi

**函数原型**

OPERATE\_RET hwl\_bnw\_station\_get\_conn\_ap\_rssi(OUT SCHAR\_T
\*rssi);

**功能说明**

当产品形态为有线+无线时，此接口用于获取wifi与router之间的 rssi值。

**参数说明**

| **参数名称** | **如何理解**                | **参数类型** | **是否必选** | **如何设置** |
|--------------|-----------------------------|--------------|--------------|--------------|
| rssi         | rssi保存获取到的rssi 信息。 | SCHAR\_T \*  | 是           |              |

**返回值**

| **返回值** | **说明** |
|------------|----------|
| OPRT\_OK   | 获取成功 |
| 其他       | 获取失败 |

### 6.2 Wifi设备配网
- 用户需要先确定wifi网卡支持的工作模式，然后在tuya_iot_wf_xx_init接口初始化时告知sdk支持的配网模式，具体参考[tuya\_iot\_wf\_xx\_dev\_init](#tuya%5C_iot%5C_wf%5C_soc%5C_dev%5C_init) 类接口的cfg参数说明
- SDK支持smart config配网以及ap配网模式。smart config也称为快速配网，是设备的wifi处于monitor模式下，获取手机app发出的无线空口报文，sdk会解析这些报文，并从中获取配置信息，切换为station模式进行联网；ap配网过程相对复杂，是指设备开启ap热点，手机连接上，通过局域网发送配置信息，设备获取配置信息之后，切换为station模式，进行联网。

#### 6.2.1 重置设备(进入待配网模式)

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/833f073e-1584-4c4b-ad41-94e44e3e63ec.png)

- 如上图，本地重置设备后，需要重启sdk进程，才可进入配网模式。
- DEV_RESET_IFM_CB 参考常见问题说明；
- 当初始化时设置同时支持AP和Smart配网模式时，调用此接口会循环切换配网模式；

##### tuya_iot_wf_gw_unactive

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_unactive(VOID);

**功能说明**

重置网关设备，解除网关有APP的绑定关系，使得网关处于非激活(待配网)状态。

**参数说明**

|              |              |              |              |              |
|--------------|--------------|--------------|--------------|--------------|
| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### 6.2.3 AP配网

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/a7faad21-2fbb-4536-89e9-34fc3d3cb706.png)

1. 实现6.2.4需要用户实现的接口
2. 实现6.2.1逻辑，使sdk进入待配网状态
3. GET_WF_NW_STAT_CB参考

#### 6.2.4 需要用户实现的接口

##### hwl\_wf\_get\_ip

**函数原型**

OPERATE\_RET hwl\_wf\_get\_ip(IN CONST WF\_IF\_E wf,OUT NW\_IP\_S \*ip);

**功能说明**

获取wifi设备ip信息。

**参数说明**

| **参数名称** | **如何理解**                                           | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------------------------------------------------|--------------|--------------|--------------|
| wf           | WiFi 的工作模式。AP 以及 station。                     | WF\_IF\_E    | 是           |              |
| ip           | 保存获取的Ip信息结构体，信息包含IP，submask，gateway。 | NW\_IP\_S \* | 是           | 输出参数     |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


##### hwl\_wf\_get\_mac

**函数原型**

OPERATE\_RET hwl\_wf\_get\_mac(IN CONST WF\_IF\_E wf,OUT NW\_MAC\_S
\*mac);

**功能说明**

获取wifi设备mac。

**参数说明**

| **参数名称** | **如何理解**                       | **参数类型**  | **是否必选** | **如何设置** |
|--------------|------------------------------------|---------------|--------------|--------------|
| wf           | WiFi 的工作模式。AP 以及 station。 | nw\_stat\_cb  | 是           |              |
| mac          | 保存获取到的mac地址。              | NW\_MAC\_S \* | 是           | 作为输出参数 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_set\_mac

**函数原型**

OPERATE\_RET hwl\_wf\_set\_mac(IN CONST WF\_IF\_E wf,IN CONST NW\_MAC\_S
\*mac);

**功能说明**

设置wifi设备mac，可选实现。

**参数说明**

| **参数名称** | **如何理解**                       | **参数类型**  | **是否必选** | **如何设置** |
|--------------|------------------------------------|---------------|--------------|--------------|
| wf           | WiFi 的工作模式。AP 以及 station。 | WF\_IF\_E     | 是           | 输入参数     |
| mac          | 设置的mac地址。                    | NW\_MAC\_S \* | 是           | 输入参数     |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_wk\_mode\_set

**函数原型**

OPERATE\_RET hwl\_wf\_wk\_mode\_set(IN CONST WF\_WK\_MD\_E mode);

**功能说明**

设置wifi工作模式。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>mode</td>
<td><p>设置wifi 工作模式：</p>
<ol type="1">
<li><p>低功耗模式</p></li>
<li><p>混杂模式</p></li>
<li><p>station 模式</p></li>
<li><p>ap 模式</p></li>
<li><p>ap + station 模式</p></li>
</ol></td>
<td>WF_WK_MD_E</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_wk\_mode\_get

**函数原型**

OPERATE\_RET hwl\_wf\_wk\_mode\_get(OUT WF\_WK\_MD\_E \*mode);

**功能说明**

获取wifi工作模式。

**参数说明**

| **参数名称** | **如何理解**                                     | **参数类型**  | **是否必选** | **如何设置** |
|--------------|--------------------------------------------------|---------------|--------------|--------------|
| mode         | 同 hwl\_wf\_wk\_mode\_set 中 参数mode 含义一致。 | WF\_WK\_MD\_E | 是           | 输出参数     |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_station\_connect

**函数原型**

OPERATE\_RET hwl\_wf\_station\_connect(IN CONST CHAR\_T \*ssid,IN CONST
CHAR\_T \*passwd);

**功能说明**

设置wifi设备建立与AP/热点/wifi路由器 的连接。

**参数说明**

| **参数名称** | **如何理解**                        | **参数类型** | **是否必选** | **如何设置** |
|--------------|-------------------------------------|--------------|--------------|--------------|
| ssid         | 连接 ap/热点/wifi路由器的ssid。     | 字符串       | 是           |              |
| password     | 连接 ap/热点/wifi路由器的password。 |              | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_station\_disconnect

**函数原型**

OPERATE\_RET hwl\_wf\_station\_disconnect(VOID);

**功能说明**

断开wifi设备与AP/热点/wifi路由器 的连接。

**参数说明**

|              |              |              |              |              |
|--------------|--------------|--------------|--------------|--------------|
| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_station\_get\_conn\_ap\_rssi

**函数原型**

OPERATE\_RET hwl\_wf\_station\_get\_conn\_ap\_rssi(OUT SCHAR\_T \*rssi);

**功能说明**

获取作为station 模式的wifi设备与AP连接的信号强度。

**参数说明**

| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------|--------------|--------------|--------------|
| rssi         | 获取到的rssi | SCHAR\_T\*   | 是           | 输出参数     |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_station\_stat\_get

**函数原型**

OPERATE\_RET hwl\_wf\_station\_stat\_get(OUT WF\_STATION\_STAT\_E
\*stat);;

**功能说明**

获取station模式下wifi设备的连接状态。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>stat</td>
<td><p>Station模式下WiFi 与 ap 连接状态。</p>
<ol type="1">
<li><p>未连接</p></li>
<li><p>正在连接</p></li>
<li><p>密码不对</p></li>
<li><p>ap 未发现</p></li>
<li><p>连接失败</p></li>
<li><p>成功连接</p></li>
<li><p>成功获取</p></li>
</ol></td>
<td>WF_STATION_STAT_E *</td>
<td>是</td>
<td>输出参数</td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_set\_country\_code

**函数原型**

OPERATE\_RET hwl\_wf\_set\_country\_code(IN CONST CHAR\_T
\*p\_country\_code);

**功能说明**

设置wifi 的国家码。

**参数说明**

| **参数名称**     | **如何理解**                                          | **参数类型** | **是否必选** | **如何设置** |
|------------------|-------------------------------------------------------|--------------|--------------|--------------|
| p\_country\_code | wifi 设备不同的国家工作的频率以及信道信号强度不相同。 | CHAR\_T \*   | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### 6.2.5 需要用户实现的接口(AP配网)

##### hwl\_wf\_ap\_start

**函数原型**

OPERATE\_RET hwl\_wf\_ap\_start(IN CONST WF\_AP\_CFG\_IF\_S \*cfg);

**功能说明**

启动wifi ap热点。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>cfg</td>
<td><p>启动作为ap 模式下的配置参数。包含：</p>
<ol type="1">
<li><p>ssid</p></li>
<li><p>password</p></li>
<li><p>信道</p></li>
<li><p>加密方式</p></li>
<li><p>是否隐藏 ssid</p></li>
<li><p>最大连接数</p></li>
</ol></td>
<td>WF_AP_CFG_IF_S *</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_ap\_stop

**函数原型**

OPERATE\_RET hwl\_wf\_ap\_stop(VOID);

**功能说明**

停止wifi ap热点。

**参数说明**

|              |              |              |              |              |
|--------------|--------------|--------------|--------------|--------------|
| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### 6.2.5 EZ配网

#### 6.2.6 用户需实现的接口(EZ配网)

##### hwl\_wf\_all\_ap\_scan

**函数原型**

OPERATE\_RET hwl\_wf\_all\_ap\_scan(OUT AP\_IF\_S \*\*ap\_ary,OUT
UINT\_T \*num);

**功能说明**

扫描所有ap。

**参数说明**

| **参数名称** | **如何理解**              | **参数类型**   | **是否必选** | **如何设置** |
|--------------|---------------------------|----------------|--------------|--------------|
| ap\_ary      | 当前环境ap 信息列表。     | AP\_IF\_S \*\* | 是           | 作为输出参数 |
| num          | 当前环境ap 信息列表长度。 | UINT\_T        | 是           | 作为输出参数 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_assign\_ap\_scan

**函数原型**

OPERATE\_RET hwl\_wf\_assign\_ap\_scan(IN CONST CHAR\_T \*ssid,OUT
AP\_IF\_S \*\*ap);

**功能说明**

扫描指定的ap。

**参数说明**

| **参数名称** | **如何理解**       | **参数类型**   | **是否必选** | **如何设置** |
|--------------|--------------------|----------------|--------------|--------------|
| ssid         | 指定的ap 的 ssid。 | 字符串         | 是           | 输入参数     |
| ap           | 获取指定ap信息。   | AP\_IF\_S \*\* | 是           | 输出         |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_release\_ap

**函数原型**

OPERATE\_RET hwl\_wf\_release\_ap(IN AP\_IF\_S \*ap);

**功能说明**

"5.2.1"、"5.2.2"过程中分配资源的释放处理。

**参数说明**

| **参数名称** | **如何理解**                                                                                   | **参数类型** | **是否必选** | **如何设置** |
|--------------|------------------------------------------------------------------------------------------------|--------------|--------------|--------------|
| ap           | 当ap 信息没用的时候，释放在 hwl\_wf\_all\_ap\_scan 以及 hwl\_wf\_assign\_ap\_scan 申请的内存。 | AP\_IF\_S \* | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_set\_cur\_channel

**函数原型**

OPERATE\_RET hwl\_wf\_set\_cur\_channel(IN CONST BYTE\_T chan);

**功能说明**

设置wifi 工作信道。

**参数说明**

| **参数名称** | **如何理解**         | **参数类型** | **是否必选** | **如何设置** |
|--------------|----------------------|--------------|--------------|--------------|
| chan         | 要设置的wifi工作信道 | BYTE\_T      | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_get\_cur\_channel

**函数原型**

OPERATE\_RET hwl\_wf\_get\_cur\_channel(OUT BYTE\_T \*chan);

**功能说明**

获取当前工作信道。

**参数说明**

| **参数名称** | **如何理解**                               | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------------------------------------|--------------|--------------|--------------|
| nw\_stat\_cb | 网络改变回调函数，回调函数的参数为网络状态 | nw\_stat\_cb | 是           | 详见 demo。  |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### hwl\_wf\_sniffer\_set

**函数原型**

OPERATE\_RET hwl\_wf\_sniffer\_set(IN CONST BOOL\_T en,IN CONST
SNIFFER\_CALLBACK cb);

**功能说明**

wifi设备混杂模式设置。

**参数说明**

| **参数名称** | **如何理解**                                        | **参数类型**      | **是否必选** | **如何设置** |
|--------------|-----------------------------------------------------|-------------------|--------------|--------------|
| en           | 使能/关闭 wifi 的sniffer模式。                      | nw\_stat\_cb      | 是           |              |
| cb           | 通知回调函数。抓取到的空中802.11数据包包含frame头。 | SNIFFER\_CALLBACK | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


#### 6.2.7 其他配网方式

- 此类配网设备用户层通过其他方式获取路由器的ssid、passwd和配网token，直接调用接口申请设备激活
- 二维码配网，设备通过摄像头扫描TuyaApp上生成的二维码获取ssid/passwd/token信息
- 蓝牙配网，设备通过蓝牙获取TuyaApp上的二维码获取ssid/passwd/token信息
- 串口、声波配网等

##### tuya\_iot\_gw\_wf\_user\_cfg

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_wf\_user\_cfg(IN CONST CHAR\_T \*ssid,IN
CONST CHAR\_T \*passwd,IN CONST CHAR\_T \*token);

**功能说明**

当通过其他模式配网时（非ap、非smartconfig），比如摄像头的二维码配网、声波配网等，调用此接口处理。

**参数说明**

| **参数名称** | **如何理解**                   | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------------------------|--------------|--------------|--------------|
| ssid         | ap 模式下配网使用的 ssid。     | 字符串       | 是           |              |
| passwd       | ap 模式下配网使用的 password。 | 字符串       | 是           |              |
| token        | ap 模式下配网使用的 token。    | 字符串       | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


## 7 设备固件升级(OTA)

- 固件升级主要用于修复设备bug和增加设备新功能。固件升级主要分两种，第一种是设备升级，第二种是MCU升级。这里的wifi设备的固件升级均指设备升级。

- 设备升级过程涉及手机APP 、设备、涂鸦云三端
    - 手机APP：升级进度结果的展示 或者 升级消息的发起者
    - 涂鸦云： 升级过程中的管理者，负责升级固件的存储，设备升级状态的更新，升级文案推送
    - 设备: 负责接收固件，固件升级的执行者

### 7.1 固件包配置说明

- 网关或子设备配网成功后，从TuyaApp上获取设备信息里的虚拟ID，作为固件升级的白名单
- 编译出要升级的固件包，固件版本要高于设备中运行的固件版本
- 登录 https://iot.tuya.com/index 开发者平台，到对应创建的产品下，上传配置固件包
- 操作说明：https://docs.tuya.com/cn/product/ota.html

### 7.2 升级开始的方式

设备固件上传到云端后，设备不会立即收到升级消息，目前涂鸦支持以下几种方式：

- App提醒升级: 用户首次打开设备面板时，会收到升级提醒弹框，可选择升级或不升级；
- App静默升级: 即设备静默升级，设备重启后，会向云端请求一次是否有静默升级任务，有的话直接进行升级；如果用户去打开设备面板，此时会有进度框显示，此时设备是无法操作的；
- App强制升级: 用户首次打开设备面板时，会收到升级提醒弹框，只有确定可选，负责设备无法操作；
- App检测升级: 即App用户主动点击对应设备的面板，然后点击右上角进入设备信息界面，检测设备固件版本，主动更新；

### 7.3 数据交互图

- 说明：下图中上报升级进度为tuya_sdk上报，也可通过tuya_iot_upgrade_gw_notify配置为应用层上报

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/dd7261b8-9a69-42ee-a3a3-b8f21c48749c.png)

### 7.4 相关接口说明

#### tuya\_iot\_upgrade\_gw\_notify

**函数原型**

OPERATE\_RET tuya\_iot\_upgrade\_gw(IN CONST FW\_UG\_S \*fw,

IN CONST GET\_FILE\_DATA\_CB get\_file\_cb,\\

IN CONST UPGRADE\_NOTIFY\_CB upgrd\_nofity\_cb,\\

IN CONST PVOID\_T pri\_data,\\

BOOL_T notify, UINT_T download_buf_size);

**功能说明**

联网模块固件升级处理接口。

**参数说明**

| **参数名称**      | **如何理解**                                       | **参数类型**         | **是否必选** | **如何设置**                 |
| ----------------- | -------------------------------------------------- | -------------------- | ------------ | ---------------------------- |
| fw                | 固件信息                                           | FW\_UG\_S 结构体指针 | 是           |                              |
| get\_file\_cb     | 下载内容存储                                       | 回调函数             | 是           |                              |
| upgrd\_nofity\_cb | 通知应用的升级状态                                 | 回调函数             | 是           |                              |
| pri\_data         | 传递给 get\_file\_cb 以及 upgrd\_nofity\_cb 的参数 | 指针                 | 否           | 如果不传参数，则设置为NULL。 |
| notify            | 选择是否由sdk上报升级进度                             | 布尔型 | 是 | TRUE为sdk上报，Flase为应用层上报 |
| download_buf_size | 下载最大缓存，单位字节 | UINT_T                 | 否           | 传入0,sdk默认缓存大小 |

**返回值**

| **返回值** | **说明**       |
| ---------- | -------------- |
| OPRT\_OK   | 成功           |
| 错误码     | 失败返回错误码 |

### 7.5 应用层上报升级进度

当需要应用层控制升级进度上报时，请先用tuya_iot_upgrade_gw_notify接口关闭tuya_sdk上报升级进度

#### tuya\_iot\_dev\_upgd\_progress\_rept

**函数原型**

OPERATE_RET tuya_iot_dev_upgd_progress_rept(IN CONST UINT_T percent, 

IN CONST CHAR_T *devid,

IN CONST DEV_TYPE_T tp);

**功能说明**

上报升级进度。

**参数说明**

| **参数名称**      | **如何理解**                                       | **参数类型**         | **是否必选** | **如何设置**                 |
| ----------------- | -------------------------------------------------- | -------------------- | ------------ | ---------------------------- |
| percent           | 升级进度值                                    | UINT_T | 是           | 0~99 |
| devid | 子设备时，传入子设备的devid; 网关时，传入NULL           | CHAR_T *     | 是           | 参考demo |
| tp | 设备类型                             | DEV_TYPE_T   | 是           | 参考demo |

**返回值**

| **返回值** | **说明**       |
| ---------- | -------------- |
| OPRT\_OK   | 成功           |
| 错误码     | 失败返回错误码 |



## 8 设备功能点(dp点)

涂鸦提供基于mqtt网络应用协议，实现设备控制和状态上报，MQTT是一个轻量的发布订阅模式消息传输协议，专门针对低带宽和不稳定网络环境的物联网应用设计。

tuya_sdk封装了mqtt协议层实现，以功能点(以下称为dp点)的形式呈现，支持数值型、布尔型、枚举型、字符串型、故障型，RAW型数据，像定义C变量一样简单。

开发者需要根据设备功能在涂鸦开发者平台创建对应的功能点，新建dp点说明:https://docs.tuya.com/cn/product/function.html

**特点**

1. 目前支持每个产品最多创建35个dp，复杂功能请用raw型数据实现;
2. obj型：布尔型(bool)、数值型(value)、字符串型(string)、枚举型(enum)、故障型(bitmap)，tuya_sdk会对连续上报的数值进行过滤，相同则不予上传；

### 8.1 设备指令下发回调

#### dev\_obj\_dp\_cb

**函数原型**

VOID dev\_obj\_dp\_cb(IN CONST TY\_RECV\_OBJ\_DP\_S \*dp);

**功能说明**

OBJ 功能点信息命令回调。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>dp</td>
<td><p>TY_RECV_OBJ_DP_S 中包含：</p>
<p>1）dp 命令类型。</p>
<p>2）dp 点控制的设备id。</p>
<p>3）当dtt_tp 为多播时，则mb_id 群组id。</p>
<p>4）功能点结构体数组长度。</p>
<p>5）功能点结构体数组。</p>
<p>参见 tuya_cloud_com_defs.h</p></td>
<td>TY_RECV_OBJ_DP_S*</td>
<td>是</td>
<td>开发者重点处理功能点结构体数组。功能点在涂鸦平台上有定义。</td>
</tr>
</tbody>
</table>

**返回值**

|            |          |
|------------|----------|
| **返回值** | **说明** |

#### dev\_raw\_dp\_cb

**函数原型**

VOID dev\_raw\_dp\_cb(IN CONST TY\_RECV\_RAW\_DP\_S \*dp);

**功能说明**

透传类功能点信息命令回调。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>dp</td>
<td><p>该结构体中包含：</p>
<p>1）dp 命令类型。</p>
<p>2）dp 点控制的设备id。</p>
<p>3）当dtt_tp 为多播时，则mb_id 群组id。</p>
<p>4）透传数据字节个数。</p>
<p>5）透传数据。</p>
<p>参见 tuya_cloud_com_defs.h</p></td>
<td>TY_RECV_RAW_DP_S*</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

|            |          |
|------------|----------|
| **返回值** | **说明** |

#### dev\_dp\_query\_cb

**函数原型**

VOID dev\_dp\_query\_cb(IN CONST TY\_DP\_QUERY\_S \*dp\_qry);

**功能说明**

设备特定数据查询入口。可选实现。

**参数说明**

| **参数名称** | **如何理解** | **参数类型**       | **是否必选** | **如何设置** |
|--------------|--------------|--------------------|--------------|--------------|
| dp\_qry      |              | TY\_DP\_QUERY\_S\* | 是           |              |

**返回值**

|            |          |
|------------|----------|
| **返回值** | **说明** |

### 8.2 设备状态上报

#### dev\_report\_dp\_json\_async

**函数原型**

OPERATE\_RET dev\_report\_dp\_json\_async(IN CONST CHAR\_T \*dev\_id,IN
CONST TY\_OBJ\_DP\_S \*dp\_data,IN CONST UINT\_T cnt);

**功能说明**

异步方式，上报功能点信息。

**参数说明**

| **参数名称** | **如何理解**            | **参数类型**                  | **是否必选** | **如何设置**                                                          |
|--------------|-------------------------|-------------------------------|--------------|-----------------------------------------------------------------------|
| dev\_id      | 设备 id                 | 字符串                        | 是           | 如果是子设备，则该参数为子设备的 id。如果是网关/mcu，则该参数为NULL。 |
| dp\_data     | 功能点数据结构体        | TY\_OBJ\_DP\_S 结构体数组指针 | 是           |                                                                       |
| cnt          | dp\_data 结构体数组个数 | UINT\_T                       | 是           |                                                                       |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### dev\_report\_dp\_raw\_sync

**函数原型**

OPERATE\_RET dev\_report\_dp\_raw\_sync (IN CONST CHAR\_T \*dev\_id, IN
CONST BYTE\_T dpid, IN CONST BYTE\_T \*data,IN CONST UINT\_T len, IN
CONST UINT\_T timeout);

**功能说明**

设备透传数据同步上报接口，由调用者保障数据上报的可靠性

**参数说明**

| **参数名称** | **如何理解**     | **参数类型** | **是否必选** | **如何设置**                                                          |
|--------------|------------------|--------------|--------------|-----------------------------------------------------------------------|
| dev\_id      | 设备 id          | 字符串       | 是           | 如果是子设备，则该参数为子设备的 id。如果是网关/mcu，则该参数为NULL。 |
| dpid         | 功能点id         | UINT\_T      | 是           |                                                                       |
| data         | 数据             | BYTE\_T \*   | 是           |                                                                       |
| len          | 数据长度         | UINT\_T      | 是           |                                                                       |
| timeout      | 函数柱塞超时时间 | UINT\_T      | 是           | 单位为秒                                                              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### dev\_report\_dp\_stat\_sync

**函数原型**

OPERATE\_RET dev\_report\_dp\_raw\_sync (IN CONST CHAR\_T \*dev\_id,IN
CONST TY\_OBJ\_DP\_S \*dp\_data, IN CONST UINT\_T cnt,IN CONST UINT\_T
timeout);

**功能说明**

设备结构化数据同步上报接口，由调用者保障数据上报的可靠性，通常用于统计类数据的上报。

**参数说明**

| **参数名称** | **如何理解**         | **参数类型**      | **是否必选** | **如何设置**                                                          |
|--------------|----------------------|-------------------|--------------|-----------------------------------------------------------------------|
| dev\_id      | 设备 id              | 字符串            | 是           | 如果是子设备，则该参数为子设备的 id。如果是网关/mcu，则该参数为NULL。 |
| dp\_data     | 功能点信息结构体数组 | TY\_OBJ\_DP\_S \* | 是           |                                                                       |
| cnt          | Dp状态数组长度       | UINT\_T           | 是           |                                                                       |
| timeout      | 函数柱塞超时时间     | UINT\_T           | 是           | 单位为秒                                                              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


## 9 子设备管理

### 9.1 子设备配网

#### 9.1.1 数据交互图
- 此图为从手机APP端添加子设备；
- 网关端也可调用tuya_iot_gw_bind_dev_attr直接绑定
- sub_device 和 gateway之间通信协议由用户实现

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/3f6b174d-a1f8-49aa-b1f4-d2424a4e2bad.png)



#### 9.1.2 接口&回调说明

##### gw\_add\_dev\_cb

**函数原型**

BOOL\_T gw\_add\_dev\_cb(IN CONST GW\_PERMIT\_DEV\_TP\_T tp,IN CONST
BOOL\_T permit,IN CONST UINT\_T timeout);

**功能说明**

当添加子设备时，在此回调中实现使能设备入网功能。

**参数说明**

| **参数名称** | **如何理解**                                    | **参数类型**           | **是否必选** | **如何设置**                  |
|--------------|-------------------------------------------------|------------------------|--------------|-------------------------------|
| tp           | 子设备类型。目前仅支持zigbee，RF433，以及 BLE。 | GW\_PERMIT\_DEV\_TP\_T | 是           |                               |
| permit       | 是否允许添加子设备                              | BOOL\_T                | 是           | **TRUE为允许；FALSE为禁止。** |
| timeout      | 配网超时时间                                    | UINT\_T                | 是           | **单位为秒**                  |

**返回值**

| **返回值** | **说明** |
|------------|----------|
| TRUE       | 操作成功 |
| FALSE      | 失败     |

##### gw\_del\_cb

**函数原型**

VOID gw\_del\_cb(IN CONST CHAR\_T \*dev\_id);

**功能说明**

当子设备被删除时，通过此回调通知用户。

**参数说明**

| **参数名称** | **如何理解**         | **参数类型** | **是否必选** | **如何设置** |
|--------------|----------------------|--------------|--------------|--------------|
| dev\_id      | 被删除子设备的 id 。 | 字符串       | 是           |              |

**返回值**

|            |          |
|------------|----------|
| **返回值** | **说明** |

##### tuya\_iot\_gw\_bind\_dev

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_bind\_dev(IN CONST GW\_PERMIT\_DEV\_TP\_T
tp,IN CONST USER\_DEV\_DTL\_DEF\_T uddd, IN CONST CHAR\_T \*id,IN CONST
CHAR\_T \*pk,IN CONST CHAR\_T \*ver);

**功能说明**

网关绑定子设备接口。当涂鸦app使能网关添加子设备时，调用此接口将发现的子设备绑定。

**参数说明**

| **参数名称** | **如何理解**                               | **参数类型** | **是否必选** | **如何设置**                         |
|--------------|--------------------------------------------|--------------|--------------|--------------------------------------|
| tp           | 子设备类型                                 | BYTE\_T      | 是           | 目前有三种类型：zigbee，RF433，BLE。 |
| uddd         | 设备类型，用户自己定义，区分不同类的设备。 | UINT\_T      | 是           |                                      |
| id           | 子设备 id                                  | 字符串       | 是           | 长度不能超过25个字节。               |
| pk           | 子设备 product key                         | 字符串       | 是           | 长度不能超过16个字节。               |
| ver          | 子设备硬件版本号                           | 字符串       |              | 长度不能超过10个字节                 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

##### gw\_bind\_ifm\_cb

**函数原型**

VOID gw\_bind\_ifm\_cb(IN CONST CHAR\_T \*dev\_id,IN CONST OPERATE\_RET
op\_ret);

**功能说明**

当子设备被bind后，通过此回调通知用户bind结果。

**参数说明**

| **参数名称** | **如何理解**      | **参数类型** | **是否必选** | **如何设置**                                |
|--------------|-------------------|--------------|--------------|---------------------------------------------|
| dev\_id      | bind的子设备 Id。 | 字符串       | 是           |                                             |
| op\_ret      | Bind 结果         | OPERATE\_RET | 是           | OPRT\_OK 表示绑定成功。其他表示 bind 失败。 |

**返回值**

|            |          |
|------------|----------|
| **返回值** | **说明** |


### 9.2 子设备固件升级(OTA)

#### 9.2.1 数据交互图
- sub_device 和 gateway设备层的通信由客户实现，这里只简单说明；
- 升级进度上报完全可由用户控制，这里只在网关给子设备发送固件包的过程中更新上报进度；
- 涂鸦智能App固件升级超时60s机制：每收到设备的一包进度更新消息，重新计时；应用层可通过控制升级进度上报以延长TuyaApp超时

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/0854b1ea-4000-4e59-843c-e5f7ff45a89c.png)

#### 9.2.2 接口&回调说明

##### dev\_ug\_inform\_cb

**函数原型**

VOID dev\_ug\_inform\_cb(IN CONST CHAR\_T \*dev\_id,IN CONST FW\_UG\_S
\*fw)

**功能说明**

子设备OTA 固件升级通知，表示服务器有可更新的子设备固件。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>dev_id</td>
<td>子设备 id。</td>
<td>字符串</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>fw</td>
<td><p>新固件信息。</p>
<p>包含：</p>
<p>1）固件下载地址。</p>
<p>2）md5校验值 。</p>
<p>3）版本号。</p>
<p>4）文件大小。</p></td>
<td>FW_UG_S *</td>
<td>是</td>
<td></td>
</tr>
</tbody>
</table>

**返回值**

|            |          |
|------------|----------|
| **返回值** | **说明** |

##### tuya\_iot\_upgrade\_dev

**函数原型**

OPERATE\_RET

OPERATE\_RET tuya\_iot\_upgrade\_dev(IN CONST CHAR\_T \*devid,

IN CONST FW\_UG\_S \*fw, \\

IN CONST GET\_FILE\_DATA\_CB get\_file\_cb,\\

IN CONST UPGRADE\_NOTIFY\_CB upgrd\_nofity\_cb,\\

IN CONST PVOID\_T pri\_data)

**功能说明**

设备固件升级接口，包含网关上的主控设备以及各终端子设备的升级。

**参数说明**

<table>
<thead>
<tr class="header">
<th><strong>参数名称</strong></th>
<th><strong>如何理解</strong></th>
<th><strong>参数类型</strong></th>
<th><strong>是否必选</strong></th>
<th><strong>如何设置</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>devid</td>
<td>设备id 字符串</td>
<td>字符串</td>
<td>是</td>
<td><p>如果升级的是子设备，则 devid 就是子设备的 devid。如果升级的是 soc/mcu，则 devid 为 NULL。</p>
<p><strong>Devid长度不能超过25个字节。</strong></p></td>
</tr>
<tr class="even">
<td>fw</td>
<td>固件信息</td>
<td>FW_UG_S 结构体指针</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>get_file_cb</td>
<td>下载内容存储</td>
<td>回调函数</td>
<td>是</td>
<td></td>
</tr>
<tr class="even">
<td>upgrd_nofity_cb</td>
<td>通知应用的升级状态</td>
<td>回调函数</td>
<td>是</td>
<td></td>
</tr>
<tr class="odd">
<td>pri_data</td>
<td>传递给 get_file_cb 以及 upgrd_nofity_cb 的参数</td>
<td>指针</td>
<td>否</td>
<td>如果不传参数，则设置为NULL。</td>
</tr>
</tbody>
</table>

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 成功           |
| 错误码     | 失败返回错误码 |

##### tuya\_iot\_gw\_subdevice\_update

**函数原型**

OPERATE\_RET tuya\_iot\_gw\_subdevice\_update (IN CONST CHAR\_T \*id,IN
CONST CHAR\_T \*ver);

**功能说明**

刷新子设备的固件版本号。

**参数说明**

| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置**               |
|--------------|--------------|--------------|--------------|----------------------------|
| dev\_id      | 子设备 id    | 字符串       | 是           | **不能超过25个字节。**     |
| ver          | 子设备版本号 | 字符串       | 是           | **长度不能超过10个字节。** |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


### 9.3 子设备在线管理
- 开启tuya_sdk的心跳检测机制

如下图为开启sdk的心跳保活机制时接口调用图

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/bbcdc435-d0c3-4837-9b6f-bcd8bc595472.png)

#### tuya\_iot\_dev\_online\_stat\_update

**函数原型**

OPERATE\_RET tuya\_iot\_dev\_online\_stat\_update(IN CONST CHAR\_T
\*dev\_id,IN CONST BOOL\_T online);

**功能说明**

网关更新子设备在线/离线状态。

**参数说明**

| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置**              |
|--------------|--------------|--------------|--------------|---------------------------|
| dev\_id      | 子设备 id    | 字符串       | 是           |                           |
| online       | 在线状态     | BOOL\_T      | 是           | TRUE为在线，FALSE为离线。 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### tuya\_iot\_sys\_mag\_hb\_init

**函数原型**

OPERATE\_RET tuya\_iot\_sys\_mag\_hb\_init(IN CONST
DEV\_HEARTBEAT\_SEND\_CB hb\_send\_cb);

**功能说明**

启动子设备心跳管理能力。

**参数说明**

| **参数名称** | **如何理解**                                                                                                                                   | **参数类型**          | **是否必选** | **如何设置** |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|--------------|--------------|
| hb\_send\_cb | 网关每隔3秒检查所有的子设备。如果子设备在心跳包超时内，子设备没有发送心跳给网关，则网关会设置子设备为离线，通过hb\_send\_cb 通知用户至少三次。 | hb\_send\_cb 回调函数 | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### tuya\_iot\_set\_dev\_hb\_timeout

**函数原型**

OPERATE\_RET tuya\_iot\_set\_dev\_hb\_timeout (IN CONST CHAR\_T
\*dev\_id,IN CONST TIME\_S hb\_timeout\_time);

**功能说明**

子设备心跳超时时间设置，若网关在超过设置时间内未收到子设备心跳，则网关会将子设备设置为离线。

**参数说明**

| **参数名称**      | **如何理解**           | **参数类型** | **是否必选** | **如何设置**                                                    |
|-------------------|------------------------|--------------|--------------|-----------------------------------------------------------------|
| dev\_id           | 子设备 id              | 字符串       | 是           |                                                                 |
| hb\_timeout\_time | 心跳超时时间，单位为秒 | TIME\_S      | 是           | 如果设置为 0xffffffff，该子设备将会被跳过心跳检查，会一直在线。 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


#### tuya\_iot\_fresh\_dev\_hb

**函数原型**

OPERATE\_RET tuya\_iot\_fresh\_dev\_hb(IN CONST CHAR\_T \*dev\_id);

**功能说明**

当网关收到子设备心跳信息，调用此函数刷新该子设备心跳状态。

**参数说明**

| **参数名称** | **如何理解** | **参数类型** | **是否必选** | **如何设置** |
|--------------|--------------|--------------|--------------|--------------|
| dev\_id      | 子设备 id    | 字符串       | 是           |              |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


### 9.4 获取子设备信息

#### tuya\_iot\_dev\_traversal

**函数原型**

DEV\_DESC\_IF\_S \*tuya\_iot\_dev\_traversal(INOUT VOID \*\*iterator);

**功能说明**

子设备遍历，通过此接口可以遍历网关下所有的子设备。

**参数说明**

| **参数名称** | **如何理解**                                 | **参数类型** | **是否必选** | **如何设置** |
|--------------|----------------------------------------------|--------------|--------------|--------------|
| iterator     | 临时保存链表，用户只需要定义一个空指针即可。 | VOID\*\*     | 是           |              |

**返回值**

| **返回值**       | **说明**                                           |
|------------------|----------------------------------------------------|
| DEV\_DESC\_IF\_S | 指向设备信息的结构体指针，用户可从中读取设备信息。 |
| NULL             | 设备信息已读完。                                   |

### 9.5 子设备群组控制

#### gw\_dev\_grp\_cb

**函数原型**

OPERATE\_RET gw\_dev\_grp\_cb(IN CONST GRP\_ACTION\_E action,IN CONST
CHAR\_T \*dev\_id,IN CONST CHAR\_T \*grp\_id);

**功能说明**

在该回调中实现组操作处理命令的实现。

**参数说明**

| **参数名称** | **如何理解**                       | **参数类型**   | **是否必选** | **如何设置**                                      |
|--------------|------------------------------------|----------------|--------------|---------------------------------------------------|
| action       | 组的操作类型。增加以及删除组操作。 | GRP\_ACTION\_E | 是           | 有APP传递过来，开发者根据该命令，执行相应的操作。 |
| dev\_id      | 子设备 Id。                        | 字符串         | 是           | 加入组grp\_id 的子设备ID。                        |
| grp\_id      | 组 id 。                           | 字符串         | 是           |                                                   |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


### 9.6 子设备场景功能

#### gw\_dev\_scene\_cb

**函数原型**

OPERATE\_RET gw\_dev\_scene\_cb(IN CONST SCE\_ACTION\_E action,IN CONST
CHAR\_T \*dev\_id,\\

IN CONST CHAR\_T \*grp\_id,IN CONST CHAR\_T \*sce\_id)

**功能说明**

在该回调中实现场景处理命令功能。

**参数说明**

| **参数名称** | **如何理解**                                     | **参数类型**   | **是否必选** | **如何设置**                                      |
|--------------|--------------------------------------------------|----------------|--------------|---------------------------------------------------|
| action       | 场景的操作类型。增加以及删除，以及执行场景操作。 | SCE\_ACTION\_E | 是           | 有APP传递过来，开发者根据该命令，执行相应的操作。 |
| dev\_id      | 子设备 Id。                                      | 字符串         | 是           |                                                   |
| grp\_id      | 组 id 。                                         | 字符串         | 是           |                                                   |
| sce\_id      | 场景 id。                                        | 字符串         | 是           |                                                   |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

## 12 日志管理

#### AddOutputTerm

**函数原型**

OPERATE_RET AddOutputTerm(IN CONST CHAR_T *name,IN CONST LOG_OUTPUT term);

**功能说明**

为tuya_sdk日志新增一个输出回调，用于将日志写到文件。

**参数说明**

| **参数名称** | **如何理解**                                     | **参数类型**   | **是否必选** | **如何设置**                                      |
|--------------|--------------------------------------------------|----------------|--------------|---------------------------------------------------|
| name       | 日志回调名称。 | 字符串 | 是           | 用于DelOutputTerm注销此回调 |
| term  | LOG_OUTPUT     | 回调         | 是           |                                                   |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |

#### DelOutputTerm

**函数原型**

VOID DelOutputTerm(IN CONST CHAR_T *name);

**功能说明**

注销AddOutputTerm接口注册的日志回调。

**参数说明**

| **参数名称** | **如何理解**                                     | **参数类型**   | **是否必选** | **如何设置**                                      |
|--------------|--------------------------------------------------|----------------|--------------|---------------------------------------------------|
| name       | 日志回调名称。 | 字符串 | 是           | 注销AddOutputTerm接口注册的日志回调 |

**返回值**
VOID

#### SetLogManageAttr
```c
#include "uni_log.h"
/***********************************************************
 * @Function:SetLogManageAttr
 * @Desc:   设置日志等级
 * @Param:  curLogLevel，可选参数：
 *          LOG_LEVEL_ERR       0  // 错误信息，程序正常运行不应发生的信息 
            LOG_LEVEL_WARN      1  // 警告信息
            LOG_LEVEL_NOTICE    2  // 需要注意的信息
            LOG_LEVEL_INFO      3  // 通知信息
            LOG_LEVEL_DEBUG     4  // 程序运行调试信息，RELEASE版本中删除
            LOG_LEVEL_TRACE     5  // 程序运行路径信息，RELEASE版本中删除
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   不设置时，sdk默认为DEBUG日志等级
***********************************************************/
OPERATE_RET SetLogManageAttr(IN CONST LOG_LEVEL curLogLevel);
```

#### SetLogManageAttr

**函数原型**

OPERATE_RET SetLogManageAttr(IN CONST LOG_LEVEL curLogLevel);

**功能说明**

设置日志等级。

**参数说明**

| **参数名称** | **如何理解**                                     | **参数类型**   | **是否必选** | **如何设置**                                      |
|--------------|--------------------------------------------------|----------------|--------------|---------------------------------------------------|
| curLogLevel       | 日志等级 | UINT | 是           | 参考uni_log.h说明 |

**返回值**

| **返回值** | **说明**       |
|------------|----------------|
| OPRT\_OK   | 操作成功       |
| 错误码     | 失败返回错误码 |


## 13 错误码说明

- 便于开发调试过程中定位问题，会持续更新。

| 错误码 | 原因                                     | 解决方法                 |
| ------ | ---------------------------------------- | ------------------------ |
| -944   | 上报功能点时，对应的DPID不在该产品下     | 登陆涂鸦IoT平台确认 |
| -916   | 设备与涂鸦云mqtt服务器长连接断开         | 排查设备端连接外网是否OK |
| -926   | 设备上报mqtt数据时，等待服务器puback超时 | 检查设备网络情况或降低上报频率 |
| -945   | DP点属性值不匹配                      | 排查上报的dp点属性值和平台定义是否一致|

## 14 常见问题(FAQ)

### 14.1 tuya_sdk会占用那些端口，用途是？
- 6668->tcp,局域网通信端口
- 6667->udp,局域网广播端口
- 8883,mqtt_tls通信端口
- https通信端口

### 14.2 固件下载过程中网络断开时，tuya_sdk会超时多久退出升级状态 ？
- 设备和https服务器断开后，tuya_sdk会尝试重新建立连接(连接超时参数30s)，
- 累积重试8次还没有连接成功，即退出升级状态。

### 14.3 设备安装固件还没完成，TuyaApp已经提示升级失败，是什么原因 ？
- 涂鸦智能APP，固件升级结果显示的机制：超时60s没有收到设备上报的进度更新时，即显示升级失败；
- 收到进度更新时，重新计时，等待60s;
- 设备端可在安装固件时，上报进度更新延长TuyaApp超时时间。

### 14.4 tuya_sdk下载固件是否支持断点续传 ？
- 支持～

### 14.5 是否有取消升级接口，比如对于电池供电的设备，当电量不足时，设备收到升级消息后，取消升级 
- OPERATE_RET tuya_iot_refuse_upgrade(IN CONST FW_UG_S *fw, IN CONST CHAR_T *dev_id);

### 14.6 设备和手机APP无法建立局域网通信 ？
- 确认下6667/6668端口是否被其他进程占用
- 确认下手机ip地址和设备ip地址是否处于同一个网段
- 确认下设备是否分享给他人，设备只可以和一个手机建立局域网通信
- 检查get_ip接口实现，传给sdk的ip字段是否正确

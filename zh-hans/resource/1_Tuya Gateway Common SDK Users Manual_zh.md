# 涂鸦网关通用 SDK 用户手册  

## 版本记录  

| **版本** | **编写/修订说明**      | **修订人** | **修订日期** | **备注** |
| -------- | ---------------------- | ---------- | ------------ | -------- |
| 1.0.0    | 创建文档               | 骆伟龙     | 2019/11/11   |          |
| 1.0.1    | 20191125内部讨论后修改 | 骆伟龙     | 2019/11/27   |          |

## 1. 概述  

网关通用 SDK 对底层 ZigBee 通信，网关和涂鸦云通信进行了封装，提供一系列函数接口给用户进行二次开发，降低了系统的开发门槛和难度，让第三方系统开发人员专注于其系统的业务功能实现。  

网关通用 SDK 能满足以下需求：  

- 扩展网关的子设备；  
- 扩展网关的功能；  
- 扩展网关的云接入；  

### 1.1. 扩展网关的子设备  

子设备的扩展分为两类：ZigBee 子设备和非 ZigBee 子设备。  

#### 1.1.1. 扩展 ZigBee 子设备  

市面上有大量的 ZigBee 子设备，涂鸦网关无法兼容所有厂商的 ZigBee 子设备。因此，用户可以使用涂鸦网关通用 SDK 进行二次开发，让未被兼容的 ZigBee 子设备能够接入涂鸦云。  

#### 1.1.2. 扩展非 ZigBee 子设备  

用户可以开发多协议网关，如 ZigBee + 443, ZigBee + BLE 等多协议组合。把涂鸦 ZigBee 模块接到自己的硬件系统，通过涂鸦网关通用 SDK 提供的函数接口，可以很方便地把自己的子设备接入到涂鸦云。能够快速开发一款既能管理涂鸦生态的 ZigBee 子设备，又能管理用户自己生态的子设备的多协议网关。  

### 1.2. 扩展网关的功能  

如果用户的网关连接着外设，并且希望可以通过 APP 控制网关的外设，可以使用涂鸦网关通用 SDK 进行二次开发来实现。    

### 1.3. 扩展网关的云接入  

子设备既入涂鸦云，同时又入其他云，实现双云控制。用户可以使用涂鸦网关通用 SDK 进行二次开发，实现其他云的接入业务。  

## 2. 总体结构  

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/ff5cb08d-1d2a-4b13-b32b-32904b151ba6.jpg)

根据总体结构，用户只需要完成第三方系统和涂鸦 SDK 之间的函数调用，网关就具备了以上的扩展功能。第三方系统作为宿主程序，负责 SDK 的初始化并且使用 SDK 的函数接口完成相应的业务功能开发。  

## 3. 前提条件  

在开发之前，用户需要首先注册涂鸦 IoT 平台账号，获取设备开发阶段的必要信息，包含产品 ID、授权码等。具体操作指导，请参见[涂鸦文档中心](https://docs.tuya.com/zh/iot)。  

### 3.1. 硬件要求  

- Flash >= 5 MB，并且有可读写分区    
- RAM >= 8 MB  

### 3.2. 软件要求  

- 支持 Linux 或 Android 系统  
- 支持 TCP/IP 协议栈  

## 4. SDK 说明  

### 4.1. SDK 获取  

涂鸦网关通用 SDK 是以 C 语言动态链接库(.so)的形式提供给用户，因此，需要用户提供相应的编译工具链来打包 SDK。  

### 4.2. 目录结构   

```  
├── bin
│   └── tuya_iot_app
├── include
│   ├── tuya_iot_gw_comm_api.h
│   └── tuya_iot_gw_comm_type.h
├── lib
│   └── libsdk_support.so
├── Makefile
└── src
    └── user_main.c 
```

| 文件名称                        | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| bin/tuya_iot_app                | 涂鸦预编译可执行文件，为涂鸦 SDK 进程，在用户调用初始化接口时启动。 |
| include/tuya_iot_gw_comm_api.h  | 定义 SDK 暴露的函数接口。                                    |
| include/tuya_iot_gw_comm_type.h | 定义调用 SDK 暴露的函数接口需要用到的数据类型。              |
| lib/libsdk_support.so           | SDK 动态链接库。                                             |
| Makefile                        | 编译示例程序的脚本。                                         |
| src/user_main.c                 | 示例程序，仅供参考使用。                                     |

## 5. 开发指南  

### 5.1. 主程序  

第三方系统程序（用户进程）作为宿主进程运行，需要调用 SDK 接口启动涂鸦 SDK 进程，并且注册相关的回调，主程序流程如图所示：  

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/f8d8c8dd-70d6-447a-b1d1-49c564b02b55.jpg)

注意，图中 `tuya_user_iot_reg_xxx_cb` 的“xxx”表示具体的回调接口，如网关回调、网关子设备管理回调等。 

### 5.2. 添加子设备  

#### 5.2.1. 步骤说明  

添加子设备主要由以下几个步骤完成。  

- 在涂鸦 IoT 平台上创建对应的子设备产品，获取 PID。  
- 用户进程注册并实现 `gw_dev_add_cb` 和 `gw_dev_bind_ifm_cb` 回调函数。  
- 用户进程实现与子设备通讯功能。  
- 用户进程收到子设备发起入网请求后，调用 `tuya_user_iot_gw_bind_dev` 接口完成绑定。  

#### 5.2.2. 流程交互图   

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/cb4f9621-27a9-4f19-8a04-41155c8fd08a.jpg)

#### 5.2.3. 伪代码  

```  
/**
 * 假设用户进程接收到子设备入网消息后，在此函数处理
 */
int join_msg_recv(dev_info_s *info)
{
	int ret = 0;
	uint_t uddd = 0x83000101;

	ret = tuya_user_iot_gw_bind_dev(uddd, info->dev_id, info->pid, info->ver);
	if (ret != 0) {
		/* 绑定失败, USER TODO */
	} else {
		/* 绑定成功, USER TODO */
	}
}

void gw_dev_add_handle(int permit, uint_t timeout)
{
	/* TODO: 允许子设备入网 */
}

void gw_dev_bind_ifm_handle(const char *dev_id, int result)
{
	/* 绑定成功, USER TODO */
}

int main(int argc, char **argv)
{
	...
	ty_gw_dev_cbs gw_dev_cbs = {
		.gw_dev_add_cb = gw_dev_add_handle,
		.gw_dev_del_cb = gw_dev_del_handle,
		.gw_dev_bind_ifm_cb = gw_dev_bind_ifm_handle
	};
	tuya_user_iot_reg_gw_dev_cb(&gw_dev_cbs);
	...
}
```

### 5.3. 子设备心跳保活  

#### 5.3.1. 步骤说明  

刷新子设备在线状态主要由以下几个步骤完成。  

- 网关要先添加子设备。  
- 子设备定时向网关发送心跳来保持在线状态。  
- 用户进程收到子设备的心跳，调用 `tuya_user_iot_fresh_dev_hb` 接口刷新子设备的在线状态。  

#### 5.3.2. 流程交互图   

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/80d9fb5f-adea-48f0-ad0c-653022dac9c9.jpg)

#### 5.3.3. 伪代码  

```  
/**
 * 假设用户进程接收到子设备心跳消息后，在此函数处理
 */
int keepalive_msg_recv(const char *dev_id)
{
	int ret = 0;

	ret = tuya_user_iot_fresh_dev_hb(dev_id);
	if (ret != 0) {
		/* 心跳刷新失败, USER TODO */
	} else {
		/* 心跳刷新成功, USER TODO */
	}
}
```

### 5.4. 状态上报  

#### 5.4.1. 步骤说明  

设备功能点状态发生变化后，要同步到涂鸦云，主要由以下几个步骤完成。  

- 子设备/网关外设功能点状态变化，把新状态上报到网关。  
- 用户进程接收到子设备/网关外设的功能点状态上报后，调用 `tuya_user_dev_report_dp_json_async` 或 `tuya_user_dev_report_dp_raw_sync` 把新状态同步到涂鸦云。  

#### 5.4.2. 流程交互图  

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/387aa14b-7861-4883-a05f-ae45252fce7d.jpg)

#### 5.4.3. 伪代码  

```  
/**
 * 假设用户进程接收到子设备/网关外设功能点状态上报，在此函数处理
 */
int report_msg_recv(const char *dev_id, int val)
{
	/* 这里以一路开关为例，构造功能点结构体上报到涂鸦云 */
	uint_t dps_cnt = 1;
	ty_obj_dp_s *dps = NULL;

	dps = (ty_obj_dp_s *)malloc(dps_cnt * sizeof(ty_obj_dp_s));
	if (dps == NULL) {
		return -1;
	}

	dps[0].dpid = 1;
	dps[0].type = PROP_BOOL;
	dps[0].value.dp_bool = val;

	ret = tuya_user_dev_report_dp_json_async(dev_id, dps_cnt, dps);
	if (ret != 0) {
		/* 状态上报失败, USER TODO */
	} else {
		/* 状态上报成功, USER TODO */
	}
}
```

### 5.5. 命令下发  

#### 5.5.1. 步骤说明  

通过 APP 控制设备，主要由以下几个步骤完成。  

- 设备必须处于在线状态。  
- 用户进程注册并实现 `dev_obj_dp_cb` 或 `dev_raw_dp_cb` 回调函数。  
- 在 APP 上控制设备，`dev_obj_dp_cb` 或 `dev_raw_dp_cb` 回调函数会被执行，用户需要在回调的实现中完成业务功能处理。  
- 子设备/网关外设收到命令后，执行相应的处理，其功能点状态会发生变化，要把新状态上报到网关。  
- 用户进程接收到子设备/网关外设的功能点状态上报后，调用 `tuya_user_dev_report_dp_json_async` 或 `tuya_user_dev_report_dp_raw_sync` 把新状态同步到涂鸦云。    

#### 5.5.2. 流程交互图  

![](https://airtake-public-data.oss-cn-hangzhou.aliyuncs.com/fe-static/tuya-docs/81dd0911-e39b-4685-92d4-411112bacf38.jpg)

#### 5.5.3. 伪代码  

```  
/**
 * 假设用户进程接收到子设备/网关外设功能点状态上报，在此函数处理
 */
int report_msg_recv(const char *dev_id, int val)
{
	/* 这里以一路开关为例，构造功能点结构体上报到涂鸦云 */
	uint_t dps_cnt = 1;
	ty_obj_dp_s *dps = NULL;

	dps = (ty_obj_dp_s *)malloc(dps_cnt * sizeof(ty_obj_dp_s));
	if (dps == NULL) {
		return -1;
	}

	dps[0].dpid = 1;
	dps[0].type = PROP_BOOL;
	dps[0].value.dp_bool = val;

	ret = tuya_user_dev_report_dp_json_async(dev_id, dps_cnt, dps);
	if (ret != 0) {
		/* 状态上报失败, USER TODO */
	} else {
		/* 状态上报成功, USER TODO */
	}
}

void dev_obj_dp_handle(const ty_recv_obj_dp_s *dp)
{
    int i = 0;
    
    for (i = 0; i < dp->dps_cnt; i++) {
        printf("dpid: %d\n", dp->dps[i].dpid);
        switch (dp->dps[i].type) {
            case PROP_BOOL:
                printf("type: bool, value: %d\n", dp->dps[i].value.dp_bool);
                break;
            case PROP_VALUE:
                printf("type: value, value: %d\n", dp->dps[i].value.dp_value);
                break;
            case PROP_ENUM:
                printf("type: enum, value: %d\n", dp->dps[i].value.dp_enum);
                break;
            case PROP_STR:
                printf("type: string, value: %s\n", dp->dps[i].value.dp_str);
                break;
        }
    }

    if (dp->cid == NULL) {
		/* 网关功能点, USER TODO */
    } else {
		/* 子设备功能点, USER TODO */
    }
}

int main(int argc, char **argv)
{
	...
	ty_gw_cbs gw_cbs = {
		.dev_obj_dp_cb = dev_obj_dp_handle,
		.dev_raw_dp_cb = dev_raw_dp_handle,
		.dev_reset_cb = dev_reset_handle
	};
	tuya_user_iot_reg_gw_cb(&gw_cbs);
	...
}
```

## 6. API 详解  

### 6.1. 公共接口  

#### 6.1.1. tuya_user_iot_start  

**接口原型**  

```  
int tuya_user_iot_start(ty_iot_attr_s *attr, ty_iot_cbs *cbs);  
```

**接口说明**  

SDK 业务初始化，在调用其他接口之前，必须先调用此接口。  

**参数说明**  

| **参数** | **数据类型**    | **方向** | **说明**                                      |
| -------- | --------------- | -------- | --------------------------------------------- |
| attr     | ty_iot_attr_s * | 输入     | 设置 SDK 在初始化需要的信息                   |
| cbs      | ty_iot_cbs *    | 输入     | 设置 SDK 在初始化要调用的第三方系统的回调函数 |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

**参数附加说明**  

```  
typedef struct {
	char  *tuya_sdk_app;
	char  *storage_path;
	char  *img_path;
	char  *tty_device;
	uint_t tty_baudrate;
	char  *eth_ifname;
	char  *wifi_ifname;
	char  *ver;
	int    log_level;
} ty_iot_attr_s;
```

- `tuya_sdk_app[可选]`: 涂鸦 SDK 程序的绝对路径，调用初始化接口会启动该程序。若不指定路径，默认在当前路径寻找 tuya_iot_app。  
- storage_path: 数据库的存储路径。  
- img_path: 固件存储的路径，COO 以及子设备升级时，从该路径获取固件文件。  
- tty_device: 与 ZigBee COO 模块通信的串口设备，如 /dev/ttyS1。  
- tty_baudrate: 与 ZigBee COO 模块通信的串口波特率，仅支持 115200 和 57600。波特率为 115200 需要接硬件流控，而 57600 则不需要。  
- eth_ifname: UDP 报文广播接口。  
- `wifi_ifname[可选]`: 带无线功能的网关，可以指定无线接口。  
- ver: 软件版本号，可通过涂鸦云进行固件升级，格式为“x.x.x”。  
- `log_level[可选]`: 设置 log 打印等级，开发阶段可开启 debug 模式。  

```  
typedef struct {
	int (*get_uuid_authkey_cb)(char *uuid, int uuid_size, char *authkey, int authkey_size);
	int (*get_product_key_cb)(char *pk, int pk_size);
	int (*dev_led_control_cb)(uint_t itime); 
	int (*sdk_process_upgrade_cb)(char *img_file);
	void (*sdk_process_reboot_cb)(void);
} ty_iot_cbs;
```

请参见 “**SDK 初始化回调**”章节。  

#### 6.1.2. tuya_user_iot_end  

**接口原型**  

```  
void tuya_user_iot_end(void);  
```

**接口说明**  

程序结束时，调用此接口释放资源。  

**参数说明**  

无  

**返回值说明**  

无  

#### 6.1.3. tuya_user_dev_report_dp_json_async  

**接口原型**  

```  
int tuya_user_dev_report_dp_json_async(const char *dev_id, uint_t dps_cnt, ty_obj_dp_s *dps);  
```

**接口说明**  

当设备状态发生变化时，调用此接口上报(obj)格式功能点。    

**参数说明**  

| **参数** | **数据类型**  | **方向** | **说明**      |
| -------- | ------------- | -------- | ------------- |
| dev_id   | char *        | 输入     | 设备的唯一 ID |
| dps_cnt  | uint_t        | 输入     | 功能点数量    |
| dps      | ty_obj_dp_s * | 输入     | 功能点数据    |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

**参数附加说明**  

```  
typedef struct {
	byte_t dpid;
	byte_t type;
	union {
		int_t dp_value;
		uint_t dp_enum;
		char *dp_str;
		int dp_bool;
		uint_t dp_bitmap;
	} value;
	uint_t time_stamp;
} ty_obj_dp_s;
```

- dpid: 在涂鸦 IoT 平台上定义的功能点编号。  
- type: 功能点的数据类型，支持的数据类型请参见[涂鸦文档中心](https://docs.tuya.com/zh/iot)的功能点定义。  
- value: 功能点的值，数据类型与 `type` 一致。  
- time_stamp:  时间戳，值为 0 时采用当前的时间。  

#### 6.1.4. tuya_user_dev_report_dp_raw_sync  

**接口原型**  

```  
int tuya_user_dev_report_dp_raw_sync(const char *dev_id, uint_t dpid, uint_t len, byte_t *data);
```

**接口说明**  

当设备状态发生变化时，调用此接口上报(raw)格式功能点。    

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**                          |
| -------- | ------------ | -------- | --------------------------------- |
| dev_id   | char *       | 输入     | 设备的唯一 ID                     |
| dpid     | uint_t       | 输入     | 在涂鸦 IoT 平台上定义的功能点编号 |
| len      | uint_t       | 输入     | 透传数据长度                      |
| data     | byte_t *     | 输入     | 透传数据                          |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.1.5. tuya_user_iot_gw_bind_dev  

**接口原型**  

```  
int tuya_user_iot_gw_bind_dev(uint_t uddd, const char *dev_id, const char *pid, const char *ver);
```

**接口说明**  

当涂鸦 APP 使能网关添加子设备，网关与子设备关联后，调用此接口将发现的子设备绑定。     

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**                                           |
| -------- | ------------ | -------- | -------------------------------------------------- |
| uddd     | uint_t       | 输入     | 用户自定义，用于区分不同类型的设备，最高位必须是 1 |
| dev_id   | char *       | 输入     | 子设备的唯一 ID                                    |
| pid      | char *       | 输入     | 在涂鸦 IoT 平台上创建子设备产品得到的 PID          |
| ver      | char *       | 输入     | 子设备的软件版本，用于固件升级                     |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.1.6. tuya_user_iot_gw_unbind_dev  

**接口原型**  

```  
int tuya_user_iot_gw_unbind_dev(const char *dev_id);
```

**接口说明**  

调用此接口将子设备从网关上移除。       

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**        |
| -------- | ------------ | -------- | --------------- |
| dev_id   | char *       | 输入     | 子设备的唯一 ID |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.1.7. tuya_user_iot_fresh_dev_hb  

**接口原型**  

```  
int tuya_user_iot_fresh_dev_hb(const char *dev_id);
```

**接口说明**  

网关收到子设备的心跳，调用此接口刷新子设备在线状态。     

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**        |
| -------- | ------------ | -------- | --------------- |
| dev_id   | char *       | 输入     | 子设备的唯一 ID |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.1.8. tuya_user_iot_scene_linkage_exec  

**接口原型**  

```  
int tuya_user_iot_scene_linkage_exec(ty_scene_linkage_attr *attr);
```

**接口说明**  

调用此接口触发联动的相应动作。   

**参数说明**  

| **参数** | **数据类型**            | **方向** | **说明**               |
| -------- | ----------------------- | -------- | ---------------------- |
| attr     | ty_scene_linkage_attr * | 输入     | 触发联动动作需要的信息 |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

**参数附加说明**  

```  
typedef struct {
	char *rule_id;
} ty_scene_linkage_attr;
```

- rule_id: 联动规则 ID。  

#### 6.1.9. tuya_user_iot_reg_gw_cb

**接口原型**  

```  
void tuya_user_iot_reg_gw_cb(ty_gw_cbs *cbs);
```

**接口说明**  

注册网关的回调。  

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**             |
| -------- | ------------ | -------- | -------------------- |
| cbs      | ty_gw_cbs *  | 输入     | 网关的一系列回调函数 |

**返回值说明**  

无

**参数附加说明**  

```  
typedef struct {
	void (*dev_obj_dp_cb)(ty_recv_obj_dp_s *dp);
	void (*dev_raw_dp_cb)(ty_recv_raw_dp_s *dp);
	void (*dev_reset_cb)(const char *dev_id, int type);
} ty_gw_cbs;
```

请参见“**网关回调**”章节。  

#### 6.1.10. tuya_user_iot_reg_gw_dev_cb  

**接口原型**  

```  
void tuya_user_iot_reg_gw_dev_cb(ty_gw_dev_cbs *cbs);
```

**接口说明**  

注册网关子设备管理的回调。    

**参数说明**  

| **参数** | **数据类型**    | **方向** | **说明**                       |
| -------- | --------------- | -------- | ------------------------------ |
| cbs      | ty_gw_dev_cbs * | 输入     | 网关子设备管理的一系列回调函数 |

**返回值说明**  

无

**参数附加说明**  

```  
typedef struct {
	void (*gw_dev_add_cb)(int permit, uint_t timeout);
	void (*gw_dev_del_cb)(const char *dev_id);
	void (*gw_dev_bind_ifm_cb)(const char *dev_id, int result);
} ty_gw_dev_cbs;
```

请参见“**网关子设备管理回调**”章节。 

### 6.2. SDK 初始化回调  

以下的回调函数需要在 `tuya_user_iot_start` 接口上注册。  

#### 6.2.1. get_uuid_authkey_cb  

**接口原型**  

```  
int get_uuid_authkey_cb(char *uuid, int uuid_size, char *authkey, int authkey_size);
```

**接口说明**  

用户需要在此回调中把在涂鸦 IoT 平台上创建产品获取到的 uuid 和 authkey 赋值到 uuid 和 authkey 变量中。    

**参数说明**  

| **参数**     | **数据类型** | **方向** | **说明**                |
| ------------ | ------------ | -------- | ----------------------- |
| uuid         | char *       | 输出     | 将 uuid 赋值给该变量    |
| uuid_size    | int          | 输入     | uuid 对应的 buf 大小    |
| authkey      | char *       | 输出     | 将 authkey 赋值给该变量 |
| authkey_size | int          | 输入     | authkey 对应的 buf 大小 |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.2.2. get_product_key_cb  

**接口原型**  

```  
int get_product_key_cb(char *pk, int pk_size);
```

**接口说明**  

用户需要在此回调中把在涂鸦 IoT 平台上创建产品获取到的 PID 赋值到 pk 变量中。  

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**            |
| -------- | ------------ | -------- | ------------------- |
| pk       | char *       | 输出     | 将 PID 赋值给该变量 |
| pk_size  | int          | 输入     | pk 对应的 buf 大小  |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.2.3. dev_led_control_cb  

**接口原型**  

```  
int dev_led_control_cb(uint_t itime); 
```

**接口说明**  

控制配网 LED 灯，用户需要在此回调实现配网 LED 的硬件控制。  

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**                                                     |
| -------- | ------------ | -------- | ------------------------------------------------------------ |
| itime    | uint_t       | 输入     | 等于 0: 配网灯灭。<br/>等于 1: 配网灯常亮。<br/>大于 1: 以 itime 的频率闪烁，单位为 ms。 |

**返回值说明**  

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

#### 6.2.4. sdk_process_reboot_cb  

**接口原型**  

```  
void sdk_process_reboot_cb(void);
```

**接口说明**  

网关重置或者 ZigBee COO 升级成功后，要去重启涂鸦 SDK，用户需要此回调中实现重启涂鸦 SDK 的业务逻辑。  

**参数说明**  

无  

**返回值说明**  

无

#### 6.2.5. sdk_process_upgrade_cb  

**接口原型**  

```  
int sdk_process_upgrade_cb(char *img_file);
```

**接口说明**  

在 APP 上执行网关升级操作时，会调用此回调接口，用户需要在此回调实现固件升级的业务逻辑。   

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**       |
| -------- | ------------ | -------- | -------------- |
| img_file | char *       | 输入     | 网关的固件文件 |

| **返回值** | **说明** |
| ---------- | -------- |
| 0          | 成功     |
| 非0        | 失败     |

### 6.3. 网关回调  

以下的回调函数需要在 `tuya_user_iot_reg_gw_cb` 接口上注册。  

#### 6.3.1. dev_obj_dp_cb  

**接口原型**  

```  
void dev_obj_dp_cb(ty_recv_obj_dp_s *dp);
```

**接口说明**  

当下发控制命令时，调用此回调接口，用户需要在此回调中实现把(obj)格式功能点解析，然后实现业务功能。 

**参数说明**  

| **参数** | **数据类型**       | **方向** | **说明**         |
| -------- | ------------------ | -------- | ---------------- |
| dp       | ty_recv_obj_dp_s * | 输入     | 控制的功能点信息 |

**返回值说明**  

无  

**参数附加说明**  

```  
typedef struct {
    byte_t cmd_tp; 
    byte_t dtt_tp;
    char *cid; 
    char *mb_id;
    uint_t dps_cnt;
    ty_obj_dp_s dps[0];
} ty_recv_obj_dp_s;
```

- cmd_tp: 指令类型。0 表示 LAN 触发，1 表示 MQTT 触发，2 表示 本地定时触发，3 表示场景联动触发，4 表示重发。  
- dtt_tp:  传输方式。0 表示单播，1 表示广播，2 表示组播，3 表示场景。  
- cid: ***cid == NULL*** 表示控制的**网关的功能点**；***cid != NULL*** 表示控制的**网关子设备的功能点**，其中 cid 是子设备的唯一 ID。  
- mb_id: 群组 ID，只有当 `dtt_tp = 2` 时，该字段才有效。  
- dps_cnt: 下发的功能点数量，也是功能点结构体数组的长度。 
- dps: 数据类型说明请参见 `tuya_user_dev_report_dp_json_async` 接口的参数附加说明。    

#### 6.3.2. dev_raw_dp_cb   

**接口原型**  

```  
void dev_raw_dp_cb(ty_recv_raw_dp_s *dp);
```

**接口说明**  

当下发控制命令时，调用此回调接口，用户需要在此回调中实现把(raw)格式功能点解析，然后实现业务功能。 

**参数说明**  

| **参数** | **数据类型**       | **方向** | **说明**         |
| -------- | ------------------ | -------- | ---------------- |
| dp       | ty_recv_raw_dp_s * | 输入     | 控制的功能点信息 |

**返回值说明**  

无  

**参数附加说明**  

```  
typedef struct {
    byte_t cmd_tp;
    byte_t dtt_tp;
    char *cid;
    byte_t dpid;
    char *mb_id;
    uint_t len;
    byte_t data[0];
} ty_recv_raw_dp_s;
```

- cmd_tp: 指令类型。0 表示 LAN 触发，1 表示 MQTT 触发，2 表示 本地定时触发，3 表示场景联动触发，4 表示重发。  
- dtt_tp:  传输方式。0 表示单播，1 表示广播，2 表示组播，3 表示场景。  
- cid: ***cid == NULL*** 表示控制的**网关的功能点**；***cid != NULL*** 表示控制的**网关子设备的功能点**，其中 cid 是子设备的唯一 ID。  
- dpid: 在涂鸦 IoT 平台上定义的功能点编号。  
- mb_id: 群组 ID，只有当 `dtt_tp = 2` 时，该字段才有效。  
- len: 透传数据的长度。 
- data: 透传数据。    

#### 6.3.3. dev_reset_cb  

**接口原型**  

```  
void dev_reset_cb(const char *dev_id, int type);
```

**接口说明**  

当在 APP 恢复子设备出产设置时，调用此回调接口，用户需要在此回调中实现子设备恢复出厂设置功能。   

**参数说明**  

| **参数** | **数据类型**       | **方向** | **说明**                                          |
| -------- | ------------------ | -------- | ------------------------------------------------- |
| dev_id   | ty_recv_obj_dp_s * | 输入     | 子设备的唯一 ID                                   |
| type     | int                | 输入     | 值为 1 表示恢复出厂设置后还清空网关本地的绑定信息 |

**返回值说明**  

无    

### 6.4. 网关子设备管理回调  

以下的回调函数需要在 `tuya_user_iot_reg_gw_dev_cb` 接口上注册。  

#### 6.4.1. gw_dev_add_cb  

**接口原型**  

```  
void gw_dev_add_cb(int permit, uint_t timeout);
```

**接口说明**  

当添加子设备时，调用此回调接口，用户需要在此回调中实现使能子设备入网功能。

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**                 |
| -------- | ------------ | -------- | ------------------------ |
| permit   | int          | 输入     | 控制是否允许子设备入网   |
| timeout  | uint_t       | 输入     | 允许配网的时间，单位为秒 |

**返回值说明**  

无     

#### 6.4.2. gw_dev_del_cb 

**接口原型**  

```  
void gw_dev_del_cb(const char *dev_id);
```

**接口说明**  

当删除子设备时，调用此回调接口，用户需要在此回调中实现删除子设备功能。  

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**        |
| -------- | ------------ | -------- | --------------- |
| dev_id   | char *       | 输入     | 子设备的唯一 ID |

**返回值说明**  

无     

#### 6.4.3. gw_dev_bind_ifm_cb  

**接口原型**  

```  
void gw_dev_bind_ifm_cb(const char *dev_id, int result);
```

**接口说明**  

当子设备绑定成功后，调用此回调接口，客户在此回调可以根据绑定的结果做业务处理。  

**参数说明**  

| **参数** | **数据类型** | **方向** | **说明**        |
| -------- | ------------ | -------- | --------------- |
| dev_id   | char *       | 输入     | 子设备的唯一 ID |

**返回值说明**  

无     

# 函数接口说明

涂鸦的SDK提供了一些函数接口给用户调用，用于参数设置和业务启动。有些函数是必须被调用的有些是可选的，本文只对可选的函数做说明，除此之外都认为是必须的。函数接口如下：

### tuya_set_storge_path
```c
/**
 * @Function: tuya_set_storge_path
 * @Description: 设置SDK 存储路径，这个目录必须已经存在，且可读可写
 * @Param: fs_storge_path , 字符串长度小于110字节
 * @Return: 0成功，否则失败
 */
extern int tuya_set_storge_path(char *fs_storge_path);
```

### tuya_log_level
```c
/**
 * @Function: tuya_log_level
 * @Description: 设置sdk 日志打印等级。
 * @Param:debug_level
 *      LOG_LEVEL_ERR       0  // 错误信息，程序正常运行不应发生的信息 
        LOG_LEVEL_WARN      1  // 警告信息
        LOG_LEVEL_NOTICE    2  // 需要注意的信息
        LOG_LEVEL_INFO      3  // 通知信息
        LOG_LEVEL_DEBUG     4  // 程序运行调试信息，RELEASE版本中删除
        LOG_LEVEL_TRACE     5  // 程序运行路径信息，RELEASE版本中删除
 * @Return:0成功，否则失败
 */
extern int tuya_log_level(TUYA_LOG_LEVEL debug_level);
```

### tuya_set_serial_port
```c
/**
 * @Function: Function
 * @Description: 设置与 zigbee coo 模块的通信的串口参数
 * @Param: name , 串口名称
 * @Param: baud_rate , 串口波特率 ,如果使115200 则需要接硬件流控，如果使57600那么不接硬件流控
 * @Return: void
 */
extern int tuya_set_serial_port(char *name,unsigned int baud_rate);
```
> [!NOTE]
> 约束条件:
> 目前通信波特率仅仅支持 115200 以及 56700 两种波特率。对于波特率为 115200 在硬件连接上需接硬件流控，而57600 则不需要。

### tuya_set_image_file_path
```c
/**
 * @Function: tuya_set_image_file_path
 * @Description: 设置升级固件存放路径.
 * @Param: path，文件目录路径，字符串长度不得超过 ？？
 * @Return: 0成功，否则失败
 */
extern int tuya_set_image_file_path(char *path);
```
> [!NOTE]
> 这个目录必须存在，在对COO以及子设备（如锁、门磁等）进行升级时，就从这个路径中获取固件文件数据。

### tuya_set_callback

```c
/**
 * @Function: tuya_set_callback
 * @Description: 设置SDK在设备入网、设备离网、上报数据时要调用的第三方系统的回调函数。
 * @Param: cb_type , 回调函数类型
 * @Param: cb_function ， 回调函数名
 * @Return: 0 成功，否则失败
 */
extern int tuya_set_callback(unsigned char cb_type, void *cb_function);
```

**cb_type 的类型说明**

| 类型                      | 说明                |
| ------------------------- | ------------------- |
| CB_CLOUD_GET_UUID_AUTHKEY | 设置uuid和authkey   |
| CB_WIFI_GET_SSID_PSK      | 设置wifi得SSID和PSK |
| CB_DEV_LED_CONTROL        | 注册led 控制函数    |
| CB_SDK_PROCESS_REBOOT     | 应用SDK的进程重启   |
| CB_CLOUD_GET_PRODUCT_KEY  | 设置网关的 PID 。   |
| CB_CLOUD_SDK_UPGRADE      | SDK升级回调         |

**cb_function 的类型说明**

1. P_USER_DEV_LED_CONTROL
```c
/**
 * @Function: P_USER_DEV_LED_CONTROL
 * @Description: 与 CB_DEV_LED_CONTROL 相对应的回调函数，实现对 LED 进行控制
 * @Param: iTime
 *         0  ,关闭配网灯；
 *         1  ,打开配网灯；
 *         >1 ,闪烁配网灯；
 * @Return: 0 成功，否则失败
 */
typedef int (*P_USER_DEV_LED_CONTROL)(unsigned int iTime);
```

2. P_CLOUD_GET_UUID_AUTHKEY
```c
/**
 * @Function: P_CLOUD_GET_UUID_AUTHKEY
 * @Description: 与 CB_CLOUD_GET_UUID_AUTHKEY 相对应的回调函数，
 *               实现传入 uuid 以及 authkey 参数到 SDK 中
 * @Param: p_uuid, 设备在涂鸦云的身份id, 需要向涂鸦商务或者项目经理申请
 * @Param: uuid_buf_size, 传入 uuid 字符串长度
 * @Param: p_authkey, 设备连接涂鸦云的认证码，需要向涂鸦商务或者项目经理申请
 * @Param: authkey_buf_size, 传入 authkey 字符串长度
 * @Return: 0 成功，否则失败
 */
typedef int (*P_CLOUD_GET_UUID_AUTHKEY)(char *p_uuid, int uuid_buf_size, \
            char *p_authkey, int authkey_buf_size);
```
> [!NOTE]
> 注意：每台设备需要唯一的uuid&authkey，需要保存在非易失区内。
> Demo中的 uuid & authkey仅仅作为测试使用，不能用作实际产品。如果用作实际产品，后续产品会不可用。

3. P_CLOUD_GET_PRODUCT_KEY
```c
/**
 * @Function: P_CLOUD_GET_PRODUCT_KEY
 * @Description: 与 CB_CLOUD_GET_PRODUCT_KEY 相对应的回调函数，实现传入 PID 参数到 SDK 中
 * @Param: product_key,字符串，从涂鸦开发者平台创建产品后获得
 * @Param: buf_size , product_key字符串长度
 * @Return: 0 成功，否则失败
 */
typedef int (*P_CLOUD_GET_PRODUCT_KEY)(char *product_key, int buf_size);
```
> [!NOTE]
> 注意：demo中的 PID 仅作测试使用。用户需要自己从涂鸦平台自行申请 PID，产品中使用自己申请的PID，如果实际产品继续使用测试用PID，后续产品会不可用

4. P_WIFI_GET_SSID_PSK
```c
/**
 * @Function: Function
 * @Description: 与 CB_WIFI_GET_SSID_PSK 相对应的回调函数，实现传入 wifi 的 ssid 以及 password 参数到 SDK 中。
 *               若需网关支持 wifi 配网，则需要设置
 * @Param: p_ssid, 字符串，wifi的ssid
 * @Param: ssid_buf_size, 传入 ssid字符串长度
 * @Param: p_psk, 字符串，Wifi 的密码
 * @Param: psk_buf_size, 传入 wifi密码字符串长度
 * @Return: 0 成功，否则失败
 */
typedef int (*P_WIFI_GET_SSID_PSK)(char *p_ssid, int ssid_buf_size, char *p_psk, int  psk_buf_size);
```

5. P_SDK_PROCESS_REBOOT
```c
/**
 * @Function: P_SDK_PROCESS_REBOOT
 * @Description: 与 CB_SDK_PROCESS_REBOOT 相对应的回调函数。重启网关进程回调
 * @Param: void
 * @Return: void
 */
typedef void (*P_SDK_PROCESS_REBOOT)(void);
```

6. P_SDK_PROCESS_UPGRADE
```c
/**
 * @Function: P_SDK_PROCESS_UPGRADE
 * @Description: 与 CB_CLOUD_SDK_UPGRADE 相对应的回调函数。升级网关程序回调
 * @Param: upgrade_file, 为从服务器端拉下的网关程序的存放路径，用户进行替换当前运行网关。
 * @Return: 0 成功，否则失败
 */
typedef int (*P_SDK_PROCESS_UPGRADE)(char *upgrade_file);
```
> [!NOTE]
> 设置回调函数时， 要将所使用的全部回调函数设置完，设置的具体源代码请可以参考发布 SDK最新版本中的《user_main.c》调用实现。
> 其中wifi设备入网功能和led控制功能是可选的，如果不需要这些功能是不需要注册的。


### tuya_sdk_start
```c
/**
 * @Function: tuya_sdk_start
 * @Description: 设置 SDK 开始运行，此时 SDK 会进行一些初始操作，
 *               如打开串口、创建并启动 ZigBee 数据接收线程等。
 * @Param: Do not edit
 * @Return: 0 成功，否则失败
 */
extern int tuya_sdk_start();
```

### tuya_sdk_end
```c
/**
 * @Function: tuya_sdk_end
 * @Description: 网关sdk停止运行
 * @Param: void
 * @Return: 0 成功，否则失败
 */
extern int tuya_sdk_end(void);
```

### tuya_set_wan_eth_name
```c
/**
 * @Function: tuya_set_wan_eth_name
 * @Description: 设置接入internet 网络设备名称。SDK 会通过该接口接入涂鸦云平台
 * @Param: eth_name
 * @Return: 0 成功，否则失败
 */
extern int tuya_set_wan_eth_name(char *eth_name);
```

### tuya_set_wifi_eth_name(可选)
```c
/**
 * @Function: tuya_set_wifi_eth_name
 * @Description: 设置 wifi 配网的网络接口。 可通过此接口进行 wifi 配网
 * @Param: eth_name
 * @Return: 0 成功，否则失败
 */
extern int tuya_set_wifi_eth_name(char *eth_name);
```

### tuya_key_handle
```c
/**
 * @Function: tuya_key_handle
 * @Description: 调用此函数触发一键配网以及重置网关功能。
 * @Param: key
 *      KEYPAD_SHORT_KEY,短按，触发一键配网功能，再次按键，停止一键配网。
 *      KEYPAD_LONG_KEY,长按消息，触发网关重置功能
 * @Return: void
 */
extern int tuya_key_handle(KEYPAD_KEY_t key);
```

### tuya_set_app_ver

说明： 设置版本号后，可通过涂鸦云进行固件升级。
```c
/**
 * @Function: Function
 * @Description:设置应用网关固件版本号
 * @Param: app_version，网关的固件版本 格式:xx.xx.xx (0<=x<=9)
 * @Return: 0-成功，其他-失败 
 */
extern int tuya_set_app_ver(const char *app_version);
```

### tuya_get_sdk_info

```c
/**
 * @Function: tuya_get_sdk_info
 * @Description: 获取tuya_sdk版本信息
 * @Param: Do not edit
 * @Return: 0-成功，其他-失败 
 */
extern int tuya_get_sdk_info(char *sdk_info);
```


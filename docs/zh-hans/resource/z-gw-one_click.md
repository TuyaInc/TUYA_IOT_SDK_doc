# 路由器的一键配网功能开发

涂鸦的设备已经走进全球千户万家

## 一键配网相关 API

### set_dev_name
```c
/**
 * @Function: set_dev_name
 * @Description: 用于设置2.4G WiFi网络接口名
 * @Param: name , 
 *         用于发送一键配网信息的wifi网络接口。Sdk会通过此接口往空中发送配网信息。
 * @Return: 0 成功，否则失败
 */
int set_dev_name(char *name);
```

### dev_get_access_token
```c
/**
 * @Function: dev_get_access_token
 * @Description: 用于获取所绑定的用户信息
 * @Param: token, 获取到用户信息
 * @Param: size,  token 缓存长度
 * @Return: OPRT_OK 成功，其他失败
 */
OPERATE_RET dev_get_access_token(CHAR_T *token, INT_T size);
```

### tuya_smart_link
```c
/**
 * @Function: tuya_smart_link
 * @Description: 开始一键配网，往空中发送AP信息
 * @Param: 见如下表格说明
 * @Return: 0 成功，否则失败
 */
int tuya_smart_link(const char *ssid, const char *password, \
                    const char *token, int pkt_intervaltime, \
                    int round_intervaltime, int pkt_basecount,\
                    int m_round_count, int b_round_count);
```

**tuya_smart_link参数说明**

| 参数名称           | 如何理解                                                    | 参数类型 |
| ------------------ | ----------------------------------------------------------- | -------- |
| ssid               | AP的 ssid                                                   | 字符串   |
| passwd             | AP的 password                                               | 字符串   |
| token              | dev_get_access_token获取到的token                           | 字符串   |
| pkt_intervaltime   | 每个包间隔时间，默认为5毫秒                                 | int      |
| round_intervaltime | 每轮间隔时间(发完一组广播/一组组播包后间隔时间,当前默认2秒) | int      |
| pkt_basecount      | 每轮发包基数(当前默认为1000包)                              | int      |
| m_round_count      | 组播发包轮数(默认为1，值为0时,则不发组播包)                 | int      |
| b_round_count      | 广播发包轮数(默认为1，值为0时,则不发广播包)                 | int      |

### send_status_stop
```c
/**
 * @Function: send_status_stop
 * @Description: 在设备调用tuya_smart_link正在发送组播包和广播包时，可以调用该接口停止发送
 * @Param: void
 * @Return: void
 */
void send_status_stop(void);
```

### tuya_iot_route_start
```c
/**
 * @Function: tuya_iot_route_start
 * @Description: 用于启动定时器，定时获取设备所绑定的用户信息
 * @Param: void
 * @Return: OPRT_OK 成功，其他失败
 */
OPERATE_RET tuya_iot_route_start(void)
```
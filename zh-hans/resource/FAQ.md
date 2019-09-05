
### 常见问题解答


1. tuya_sdk会占用那些端口，用途是？
    - 6668->tcp,局域网通信端口
    - 6667->udp,局域网广播端口
    - 8883,mqtt_tls通信端口
    - https通信端口

2. 固件下载过程中网络断开时，tuya_sdk会超时多久退出升级状态 ？

    设备和https服务器断开后，tuya_sdk会尝试重新建立连接(连接超时参数30s)，
    累积重试8次还没有连接成功，即退出升级状态。
3. 设备安装固件还没完成，TuyaApp已经提示升级失败，是什么原因 ？

    涂鸦智能APP，固件升级结果显示的机制：超时60s没有收到设备上报的进度更新时，即显示升级失败；
    收到进度更新时，重新计时，等待60s;
    设备端可在安装固件时，上报进度更新延长TuyaApp超时时间。
4. tuya_sdk下载固件是否支持断点续传 ？

    支持～

5. 是否有取消升级接口，比如对于电池供电的设备，当电量不足时，设备收到升级消息后，取消升级 

    有，[tuya_iot_refuse_upgrade](public_api.md#tuyaiotrefuseupgrade)

6. 如何确定设备处于待配网状态 或 已激活成功？

    请参考：[__gw_dev_net_status_cb](05-gw_init.md#gwdevnetstatuscb)
7. 设备和手机APP无法建立局域网通信 ？

    基本原理如下：
    ```sequence
    Title: 
    participant 手机APP
    participant Device

    Note over Device: 向6667端口广播udp包
    Note over 手机APP: 监听到设备广播包，解析出设备ip等信息
    手机APP->Device:发起tcp连接，端口6668
    Device-->手机APP:局域网通信建立成功
    ```
    排查方法：
    - 确认下6667/6668端口是否被其他进程占用
    - 确认下手机ip地址和设备ip地址是否处于同一个网段
    - 确认下设备是否分享给他人，设备只可以和一个手机建立局域网通信
    - 检查hwl_wf_get_ip接口实现，传给sdk的ip字段是否正确
    - 

8. sdk主动close mqtt问题
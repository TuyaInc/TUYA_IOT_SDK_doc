# 常见问题

1. zigbee信道信息
    有些网关存在 wifi 和 zigbee 共存的情况。而两者都工作 2.4G 频段，存在同频干扰问题。涂鸦目前提供两种解决方案。

    1）	SDK启动后，在 /tmp/zigbee_channel 文件存储 zigbee 信道信息，只需要wifi 避开即可。
    
    2）	Wifi 侧支持 PTA 。则可以使用PTA 方式。需要使用支持 PTA zigbee模块。

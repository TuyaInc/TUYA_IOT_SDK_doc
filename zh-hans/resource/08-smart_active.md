### Smart配网模式

Smart配网需要网卡支持进入sniffer模式，捕获空气中的所有无线包以获取手机APP广播的ssid,passwd,token配网信息；

### Smart配网数据链路图

```sequence
Title: 
participant TuyaApp
participant 用户层
participant tuya_sdk
participant TuyaCloud

tuya_sdk->用户层: hwl_wf_wk_mode_set(WWM_STATION)
tuya_sdk->用户层: hwl_wf_all_ap_scan
tuya_sdk->用户层: hwl_wf_wk_mode_set(WWM_SNIFFER)
tuya_sdk->用户层: create func_Sniffer thread
TuyaApp->TuyaApp:用户选择Smart模式添加设备
Note over TuyaApp: 广播发送ssid/passwd/token
tuya_sdk->用户层: hwl_wf_set_cur_channel
Note over 用户层: 切换信道捕捉报文
用户层->用户层:捕捉到合法的网络报文
用户层-->tuya_sdk:s_pSnifferCall(rev_buf,len)
tuya_sdk->tuya_sdk:解析出ssid/passwd/token
tuya_sdk->用户层: exit func_Sniffer thread
tuya_sdk->用户层: hwl_wf_wk_mode_set(WWM_STATION) 
tuya_sdk->用户层: hwl_wf_station_connect(ssid,passwd)
用户层-->tuya_sdk: return OPRT_OK
tuya_sdk->用户层: hwl_wf_station_stat_get
Note over tuya_sdk: 每隔1s查询一次网络状态
用户层-->tuya_sdk: WSS_GOT_IP
tuya_sdk->TuyaCloud: 请求设备激活
TuyaCloud-->tuya_sdk: 设备激活成功
tuya_sdk->用户层: GET_WF_NW_STAT_CB(STAT_CLOUD_CONN)\n通知设备配网成功
TuyaApp->TuyaCloud:刷新设备列表
TuyaCloud-->TuyaApp:设备列表新增设备
```

### 用户需实现的接口说明

说明：如下接口涉及wifi网卡操作，请根据设备系统实现，demo里的实现仅供参考，未必适配你的设备；tuya_sdk未对wifi网卡驱动层做限制，比如没有要求必须支持ifconfig命令。
接口位置：tuya_iot_sdk/demo_soc_dev_wifi/tuya_iot_wifi_net.c文件中

- [hwl_wf_wk_mode_set](wifi_api.md#hwlwfwkmodeset)
- [hwl_wf_wk_mode_get](wifi_api.md#hwlwfwkmodeget)
- [hwl_wf_station_connect](wifi_api.md#hwlwfstationconnect)
- [hwl_wf_station_stat_get](wifi_api.md#hwlwfstationstatget)

#### hwl_wf_all_ap_scan
```c
/***********************************************************
 * @Function:hwl_wf_all_ap_scan
 * @Desc:   扫描设备周围热点信息
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   tuya_sdk的demo使用iwlist实现，如果网卡驱动支持，可不用修改
***********************************************************/
OPERATE_RET hwl_wf_all_ap_scan(OUT AP_IF_S **ap_ary,OUT UINT_T *num)
{
    // UserTODO
    return OPRT_OK;
}
```

#### hwl_wf_set_cur_channel

```c
/***********************************************************
 * @Function:hwl_wf_set_cur_channel
 * @Desc:   设置wifi工作信道
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   
***********************************************************/
OPERATE_RET hwl_wf_set_cur_channel(IN CONST BYTE_T chan)
{
    PR_DEBUG("WIFI Set Channel:%d", chan);
    // UserTODO
    return OPRT_OK;
}
```

#### hwl_wf_get_cur_channel

```c
/***********************************************************
 * @Function:hwl_wf_get_cur_channel
 * @Desc:   获取wifi工作信道
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   
***********************************************************/
OPERATE_RET hwl_wf_get_cur_channel(OUT BYTE_T *chan)
{
    // UserTODO
    *chan = ？；
    PR_DEBUG("WIFI Get Curr Channel:%d", *chan);
    return OPRT_OK;
}
```

### tuya-sdk抓包线程
```c
/*
 *  Function: func_Sniffer 抓包线程实现
 *  Note:     Smart配网模式时，tuya_sdk自动调用，无需应用层调用
 *  Return:   NULL
 */
#pragma pack(1)
/* http://www.radiotap.org/  */
typedef struct {
    BYTE_T it_version;
    BYTE_T it_pad;
    USHORT_T it_len;
    UINT_T it_present;
}ieee80211_radiotap_header;
#pragma pack()

static volatile SNIFFER_CALLBACK s_pSnifferCall = NULL;
static volatile int s_enable_sniffer = 0;
static void * func_Sniffer(void *pReserved)
{
    PR_DEBUG("Sniffer Thread Create");

    int sock = socket(PF_PACKET, SOCK_RAW, htons(0x03));//ETH_P_ALL
    if(sock < 0)
    {
        printf("Sniffer Socket Alloc Fails %d \r\n", sock);
        perror("Sniffer Socket Alloc");
        return (void *)0;
    }

    {/* 强制绑定到WLAN_DEV 上。后续可以考虑去掉 */
        struct ifreq ifr;
        memset(&ifr, 0x00, sizeof(ifr));
        strncpy(ifr.ifr_name, WLAN_DEV , strlen(WLAN_DEV) + 1);
        setsockopt(sock, SOL_SOCKET, SO_BINDTODEVICE, (char *)&ifr, sizeof(ifr));
    }

    #define MAX_REV_BUFFER 512
    BYTE_T rev_buffer[MAX_REV_BUFFER];

    int skipLen = 26;/* radiotap 默认长度为26 */

    while((s_pSnifferCall != NULL) && (s_enable_sniffer == 1))
    {
        int rev_num = recvfrom(sock, rev_buffer, MAX_REV_BUFFER, 0, NULL, NULL);
        ieee80211_radiotap_header *pHeader = (ieee80211_radiotap_header *)rev_buffer;
        skipLen = pHeader->it_len;

#ifdef WIFI_CHIP_7601
        skipLen = 144;
#endif
        if(skipLen >= MAX_REV_BUFFER)
        {/* 有出现过header全ff的情况，这里直接丢掉这个包 */
            continue;
        }
#if 0
        {
            printf("skipLen:%d ", skipLen);
            int index = 0;
            for(index = 0; index < 180; index++)
            {
                printf("%02X-", rev_buffer[index]);
            }
            printf("\r\n");
        }
#endif
        /* wifi recv from packages from the air, and send these 
         packages to tuya-sdk with hwl_wf_sniffer_set of callback <cb> */
        if(rev_num > skipLen)
        {
            s_pSnifferCall(rev_buffer + skipLen, rev_num - skipLen);
        }
        PR_DEBUG("s_enable_sniffer");
    }

     s_pSnifferCall = NULL;

    close(sock);

    PR_DEBUG("Sniffer Proc Finish");
    return (void *)0;
}

// 开启或退出抓包线程，tuya-sdk会调用
static pthread_t sniffer_thId; // 抓包线程ID
OPERATE_RET hwl_wf_sniffer_set(IN CONST BOOL_T en,IN CONST SNIFFER_CALLBACK cb)
{
    if(en == TRUE)
    {
        PR_DEBUG("Enable Sniffer");
        hwl_wf_wk_mode_set(WWM_SNIFFER);
        s_pSnifferCall = cb;
        if(s_enable_sniffer == 1){
            return;
        }
        s_enable_sniffer = 1;
        pthread_create(&sniffer_thId, NULL, func_Sniffer, NULL);
    }else
    {
        PR_DEBUG("Disable Sniffer");
        s_enable_sniffer = 0;
        pthread_join(sniffer_thId, NULL);
        hwl_wf_wk_mode_set(WWM_STATION);
    }
    return OPRT_OK;
}
```
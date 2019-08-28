## 子设备管理

### 获取网关下所有子设备信息

#### tuya_iot_dev_traversal

```c
/***********************************************************
 * @Function:tuya_iot_dev_traversal
 * @Desc:    遍历网关下所有子设备
 * @Param:   iterator, 临时保存链表，用户只需要定义一个空指针即可
 * @Return:  TRUE: success  FALSE: fail，
***********************************************************/
DEV_DESC_IF_S *tuya_iot_dev_traversal(INOUT VOID **iterator);
```
使用说明：
```c
    DEV_DESC_IF_S * dev_if = NULL;
    VOID *iterator = NULL;
    do { 
        dev_if = (DEV_DESC_IF_S *)tuya_iot_dev_traversal(&iterator);
        if(dev_if) {
            PR_DEBUG("product_key:%s",dev_if->product_key);
            PR_DEBUG("sub device id:%s",dev_if->id);
            PR_DEBUG("sub device ver:%s",dev_if->sw_ver);
            PR_DEBUG("sub device uddd:%d",dev_if->uddd);
            PR_DEBUG("sub device type:%d",dev_if->tp); 
        } 
    }while(dev_if); 
```

#### 子设备信息数据结构
```c
#define GW_ATTACH_ATTR_LMT 4
typedef struct {
    CHAR_T id[DEV_ID_LEN+1];
    CHAR_T sw_ver[SW_VER_LEN+1];
    CHAR_T schema_id[SCHEMA_ID_LEN+1];
    CHAR_T product_key[PRODUCT_KEY_LEN+1];
    CHAR_T firmware_key[PRODUCT_KEY_LEN+1];

    USER_DEV_DTL_DEF_T uddd; // user detial type define
    DEV_TYPE_T tp;
    BOOL_T bind; // 子设备是否绑定到云端
    BOOL_T sync; // 同步本地版本和云端版本使用的
    BYTE_T attr_num;
    GW_ATTACH_ATTR_T attr[GW_ATTACH_ATTR_LMT];
    BOOL_T reset_flag; // 是用来判断子设备是否已经恢复出厂用的
}DEV_DESC_IF_S;
```
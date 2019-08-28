## 子设备组操作

### 用户回调接口

```c
/***********************************************************
 * @Function:__gw_dev_grp_infm_cb
 * @Desc:   在该回调中实现组操作处理命令的实现。
 * @Param:  action,
 * @Param:  dev_id, 子设备id
 * @Param:  grp_id, 组id 3位的十进制字符串
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
STATIC OPERATE_RET __gw_dev_grp_infm_cb(IN CONST GRP_ACTION_E action,\
                                        IN CONST CHAR_T *dev_id,\
                                        IN CONST CHAR_T *grp_id)
{
    PR_DEBUG("group inform cb. action:%d dev_id:%s grp_id:%s", action, dev_id, grp_id);
}
```
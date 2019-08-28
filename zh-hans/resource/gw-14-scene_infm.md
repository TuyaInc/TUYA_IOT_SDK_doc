## 子设备场景功能

### 用户回调接口

```c
/***********************************************************
 * @Function:__gw_dev_scene_infm_cb
 * @Desc:   在该回调中实现场景处理命令功能。
 * @Param:  action,
 * @Param:  dev_id, 子设备id
 * @Param:  grp_id, 组id    5位的十进制字符串
 * @Param:  sce_id, 场景id  3位的十进制字符串
 * @Return: OPRT_OK: success  Other: fail
***********************************************************/
STATIC OPERATE_RET __gw_dev_scene_infm_cb(IN CONST SCE_ACTION_E action,IN CONST CHAR_T *dev_id,\
                                          IN CONST CHAR_T *grp_id,IN CONST CHAR_T *sce_id)
{
    PR_DEBUG("scene inform cb. action:%d dev_id:%s grp_id:%s sce_id:%s", action, dev_id, grp_id, sce_id);
}
```
## 日志管理

#### AddOutputTerm
```c
#include "uni_log.h"
/***********************************************************
 * @Function:AddOutputTerm
 * @Desc:   为tuya_sdk日志新增一个输出回调，一般用于将日志写到文件
 * @Param:  name，日志回调名称，长度小于 ？ 字节
 * @Param:  term,日志回调函数名
 * @Return: OPRT_OK: success  Other: fail
 * @Note:   此函数必须在tuya_iot_init之后调用
***********************************************************/
OPERATE_RET AddOutputTerm(IN CONST CHAR_T *name,IN CONST LOG_OUTPUT term);
```

接口使用示例：

```c
void tuya_sdk_log_cb(const char *str)
{
    //UserTODO 用户具体实现日志输出位置，以下示例输出到文件
    FILE *log_fd;
    log_fd = fopen("debug.log", "a+"); // 采用追加方式，用户需限制文件大小
    if(log_fd == NULL){
        printf("log_fd fopen is error");
    }
    UINT16_T str_len = strlen(str);
    if( fwrite(str,sizeof(CHAR_T),strlen(str),log_fd) != str_len){
        printf("fwrite log_fd is error");
    }
    fflush(log_fd); // 冲洗缓存区，立即写到磁盘文件
    fclose(log_fd);
}

op_ret = AddOutputTerm("tuya_sdk_log",tuya_sdk_log_cb);
if(OPRT_OK != op_ret){
    PR_ERR("AddOutputTerm op_ret is %d\n",op_ret);
}
```

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
接口使用示例：
```c
OPERATE_RET op_ret = SetLogManageAttr(LOG_LEVEL_INFO);
if(OPRT_OK != op_ret){
    PR_ERR("SetLogManageAttr op_ret is %d\n",op_ret);
}
```
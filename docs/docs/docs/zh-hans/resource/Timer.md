## 定时任务

涂鸦智能提供了基本的定时能力，支持设备（包括WiFi设备，蓝牙mesh子设备，zigbee子设备）和群组。并封装了针对设备dp点的定时器信息的增删改查接口。APP通过定时接口设置好定时器信息后，硬件模块会自动根据定时要求进行预订的操作。每个定时任务下可以包含多个定时器。如下图所示：
![timer](./images/ios-sdk-timer.jpg)

定时相关的所有方法都在`TuyaHomeSdk.getTimerManagerInstance()`中

以下多个接口用到了taskName这个参数，具体可描述为一个分组，一个分组可以有多个定时器。每个定时属于或不属于一个分组，分组目前仅用于展示

### 增加定时器

##### 【 描述】

增加一个定时器

##### 【方法调用】

```java
*  增加定时器 单dp点 默认置为true  支持子设备
*  @param taskName     定时任务名称
*  @param loops        循环次数 "0000000", 每一位 0:关闭,1:开启, 从左至右依次表示: 周日 周一 周二 周三 周四 周五 周六
*  @param devId        设备Id或群组Id
*  @param dpId  			dp点id
*  @param time         定时任务下的定时钟
*  @param callback     回调
void addTimerWithTask(String taskName, String loops, String devId, String dpId, String time, final IResultStatusCallback callback);


/**
 * 增加定时器         支持子设备新接口
 * 其他参数值释义同上
 * @param dps    dp点键值对，key是dpId，value是dpValue,仅支持单dp点
 * @param callback 回调
 */
void addTimerWithTask(String taskName, String devId, String loops, Map<String, Object> dps, String time, final IResultStatusCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().addTimerWithTask("task01", "1111111",mDevId,  "1", "14:29", new IResultStatusCallback() {
    @Override
    public void onSuccess() {
        Toast.makeText(mContext, "添加定时任务成功", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "添加定时任务失败 " + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

### 获取某设备下的所有定时任务状态

##### 【描述】

获取某设备下的所有定时任务状态

##### 【方法调用】

```java
* 获取某设备下的所有定时任务状态
*
* @param devId    设备Id
* @param callback 回调
void getTimerTaskStatusWithDeviceId(String devId, final IGetDeviceTimerStatusCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().getTimerTaskStatusWithDeviceId(mDevId, new IGetDeviceTimerStatusCallback() {
    @Override
    public void onSuccess(ArrayList<TimerTaskStatus> list) {
        Toast.makeText(mContext,"获取设备的定时状态成功",Toast.LENGTH_LONG).show();
    }
    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "获取设备的定时状态失败 " + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

### 控制定时任务中所有定时器的开关状态

##### 【描述】

控制定时任务中所有定时器的开关状态

##### 【方法调用】

```java
* 控制定时任务中所有定时器的开关状态
* @param taskName 定时任务名称
* @param devId    设备Id或群组Id
* @param status   状态值 1表示开关状态开启  0开关状态关闭
* @param callback 回调
void updateTimerTaskStatusWithTask(String taskName, String devId, int status, final IResultStatusCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().updateTimerTaskStatusWithTask(taskName, mDevId, 1, new IResultStatusCallback() {
    @Override
    public void onSuccess() {
        Toast.makeText(mContext, "控制定时任务中所有定时器的开关状态成功", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "控制定时任务中所有定时器的开关状态失败 " + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

### 控制某个定时器的开关状态

##### 【描述】

控制某个定时器的开关状态

##### 【方法调用】

```java
* 控制某个定时器的开关状态
* @param taskName 定时任务名称
* @param devId    设备Id或群组Id
* @param timerId  定时钟Id
* @param isOpen   开关
* @param callback 回调
void updateTimerStatusWithTask(String taskName, String devId, String timerId, boolean isOpen, IResultStatusCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().updateTimerStatusWithTask(taskName, mDevId, timeId, false, new IResultStatusCallback() {
        @Override
        public void onSuccess() {
            Toast.makeText(mContext, "控制定时器的开关状态成功", Toast.LENGTH_LONG).show();
        }

        @Override
        public void onError(String errorCode, String errorMsg) {
            Toast.makeText(mContext, "控制定时器的开关状态失败 " + errorMsg, Toast.LENGTH_LONG).show();
        }
    });
```

### 删除定时器

##### 【描述】

删除定时器

##### 【方法调用】

```java
* 删除定时器
* @param taskName 定时任务名称
* @param devId    设备Id或群组Id
* @param timerId  定时钟Id
* @param callback 回调
void removeTimerWithTask(String taskName, String devId, String timerId, IResultStatusCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().removeTimerWithTask(taskName, mDevId, timeId, new IResultStatusCallback() {
    @Override
    public void onSuccess() {
        Toast.makeText(mContext, "删除定时成功", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "删除定时失败" + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

### 更新定时器的状态

##### 【描述】

更新定时器的状态 该接口可以修改一个定时器的所有属性。

##### 【方法调用】

```java
* 更新定时器的状态
* @param taskName 定时任务名称
* @param loops    循环次数 如每周每天传”1111111”
* @param devId    设备Id或群组Id
* @param timerId  定时钟Id
* @param dpId     dp点id
* @param time     定时时间
* @param isOpen	  是否开启 
* @param callback 回调
void updateTimerWithTask(String taskName, String loops, String devId, String timerId, String dpId, String time, boolean isOpen, final IResultStatusCallback callback);


/**
 * 更新定时器的状态
 * @param taskName 定时任务名称
 * @param devId    设备Id或群组id
 * @param timerId  定时钟Id
 * @param loops    循环次数
 * @param instruct 定时dp点数据,只支持单dp点 json格式 如:   [{
 *                 "time": "20:00",
 *                 "dps": {
 *                 "1": true
 *                 }]
 * @param callback 回调
 */
void updateTimerWithTask(String taskName, String loops, String devId, String timerId, String instruct, final IResultStatusCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().updateTimerWithTask(taskName,"0011001", mDevId, timeId,  "[{"time": "20:00", "dps": { "1": true},{"time": "22:00","dps": {"2": true}]",  new IResultStatusCallback() {
    @Override
    public void onSuccess() {
        Toast.makeText(mContext, "更新定时器属性成功", Toast.LENGTH_LONG).show();
    }
    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "更新定时器属性失败" + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

### 获取定时任务下所有定时器

##### 【描述】

获取定时任务下所有定时器

##### 【方法调用】

```java
* 获取定时任务下所有定时器
* @param taskName 定时任务名称
* @param devId    设备Id或群组Id
* @param callback 回调
void getTimerWithTask(String taskName, String devId, final IGetTimerWithTaskCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().getTimerWithTask(taskName, mDevId, new IGetTimerWithTaskCallback() {
    @Override
    public void onSuccess(TimerTask timerTask) {
        Toast.makeText(mContext, "获取定时任务下的定时 成功", Toast.LENGTH_LONG).show();
    }
    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "获取定时任务下的定时 失败" + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

### 获取设备所有定时任务下所有定时器

##### 【描述】

获取设备所有定时任务下所有定时器

##### 【方法调用】

```java
* 获取设备所有定时任务下所有定时器
* @param devId    设备Id或群组Id
* @param callback 回调
void getAllTimerWithDeviceId(String devId, final IGetAllTimerWithDevIdCallback callback);
```

##### 【代码范例】

```java
TuyaHomeSdk.getTimerManagerInstance().getAllTimerWithDeviceId(mDevId, new IGetAllTimerWithDevIdCallback() {
    @Override
    public void onSuccess(ArrayList<TimerTask> taskArrayList) {
        Toast.makeText(mContext, "获取设备下的定时 成功", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onError(String errorCode, String errorMsg) {
        Toast.makeText(mContext, "获取设备下的定时 失败" + errorMsg, Toast.LENGTH_LONG).show();
    }
});
```

## 
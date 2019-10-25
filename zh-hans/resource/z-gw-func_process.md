# 函数调用流程

在SDK的函数调具有严格的时序要求与前提条件，后一步执行的前提条件是上一步执行成功，这样才能确保 SDK 的正常运行。系统设置函数调用的时序图如下图所示：

![func_process](images/func_process.png)
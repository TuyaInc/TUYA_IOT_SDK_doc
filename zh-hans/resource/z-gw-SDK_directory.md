
### SDK文件目录说明

```shell
.
├── include
│   ├── tuya_export_gateway.h   # 定义 SDK 调用接口函数
│   └── tuya_export_type.h  # 定义 SDK 调用接口函数所所使用到的类型定义等。
├── libs
│   └── libsdk_support.a    # SDK 静态链接库
├── Makefile
├── readme.txt
└── user_main.c

2 directories, 6 files
```

SDK 以头文件以及静态库的形式，提供给开发者。开发者只需在自己的工程中包含响应的头文件以及 .a 的静态库即可。
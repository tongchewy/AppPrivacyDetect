# privacy_detect

基于 frida 的隐私检测工具

# 使用方法

```
python privacy_detect.py {apk包名}
```


```
python privacy_detect.py {apk包名} -ns -f demo01.xls

-ns 不显示日志，默认显示。
-f 保存app行为堆栈信息到execl里，默认不保存。
```

```
python privacy_detect.py {apk包名} -t 3

-t 延迟hook，hook应用自己的函数或含壳时，建议使用setTimeout并给出适当的延时(1-5s，需要根据不同app进行调整)。以免hook失败。默认不延迟。
```

```
-ia：使用attach hook

假如还是hook不上，可以使用-ia，指定包名或运行进程ID。 有些包有同名进程，frida会hook失败，需要使用进程ID。

找进程ID，进入安卓运行ps -A | grep {apk包名}
```

```
-u： 扫描指定模块。与命令`-nu`互斥。多个模块用','隔开。例如：phone,permission

**模块列表：**

| 模块名 | 备注 |
| ------ | ------ |
|permission|申请权限|
|phone|电话、基站|
|system|系统信息(AndroidId/标识/content敏感信息)|
|app|其他app信息|
|location|位置信息|
|network|getNetwork|
|camera|照相机|
|bluetooth|蓝牙|
|file|文件|
|media|麦克风|
|sensor|传感器|
|custom| 用户自定义接口|

-nu：跳过扫描指定模块。与命令`-u`互斥。多个模块用','隔开。例如：phone,permission 模块列表同上
```


# 指定设备

```
python privacy_detect.py {apk包名} -s HT68R0204267
```

- -s：指定连接设备，可通过 `adb devices` 获取设备 id


# 自定义hook接口

在`hook_script.js`文件里的`customHook`方法里可自行添加需要hook的接口。

如hook`{apk包名}.HookTest`类的`getPassword`和`getUser`方法。如下：

```
hook('{apk包名}.HookTest', [
    {'methodName': 'getPassword', 'action': action, 'messages': '获取zhengjim密码'},
    {'methodName': 'getUser', 'action': action, 'messages': '获取用户名'},
]);
```

如果需要过滤参数，使用args参数，如下，只记录参数为`android_id`：
```
hook('android.provider.Settings$Secure', [
    {'methodName': 'getString', 'args': [ 'android_id' ], 'action': action, 'messages': '获取安卓ID'}
]);
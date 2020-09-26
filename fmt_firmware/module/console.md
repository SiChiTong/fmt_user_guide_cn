# Console控制台
控制台 (Console) 组件供了控制台的输入/输出功能，主要用于调试信息的打印以及命令行的交互功能。控制台可以被映射到不同的设备上，如串口，USB等。

## API
```c
fmt_err console_init(char* dev_name);
fmt_err console_mount_shell(rt_device_t dev);
fmt_err console_set_device(char* dev_name);
uint32_t console_write(const char* content, uint32_t len);
uint32_t console_printf(const char* fmt, ...);
uint32_t console_println(const char* fmt, ...);
void console_format(char* buffer, const char* fmt, ...);
```

## 设置控制台设备
调用如下函数来将控制台映射到`dev_name`对应的设备上。
```c
fmt_err console_set_device(char* dev_name);
```
控制台默认使用`serial1`设备 (TELEM2接口)。当连接QGC地面站，并在`Mavlink Console`输入任意字符，console将被自动切换到`mav_console`设备上。

当前console支持的设备名称如下：
- `serialx`： 标准串口设备，默认使用`serial1`
- `mav_console`: 基于mavlink协议的虚拟控制台设备。它将console的输入/输出数据封装为`MAVLINK_MSG_ID_SERIAL_CONTROL`的包，再通过串口或者USB进行发送，以支持QGC的`Mavlink Console`功能。

## 绑定Shell
调用如下函数将设备输入绑定到 shell 从而解析输入的系统指令。若传入的`dev=NULL`，则默认将当前`console`的设备绑定到shell。
```c
fmt_err console_mount_shell(rt_device_t dev);
```

## 控制台打印
在控制台初始化完成后，可以调用如下函数来打印信息到控制台设备。
```c
uint32_t console_printf(const char* fmt, ...);
uint32_t console_println(const char* fmt, ...);
uint32_t console_write(const char* content, uint32_t len);
```
`console_printf()`用法类似`printf()`，支持各种格式化打印，且支持中断函数中打印，如：
```c
console_printf("%f %d %x\n", 0.2, 33, 0xFF);
```
`console_write()`函数则提供了控制台数据直接写入的功能，一般用来写入非格式化数据到控制台，如:
```c
console_write(buffer, sizeof(buffer));
```
# uMCN消息订阅/发布模块
uMCN (Micro Multi-Communication Node) 提供基于订阅/发布模式的安全跨进程通信方式，为FMT Firmware 各个任务，模块之间进行信息交换的主要方式。

## API
```c
fmt_err mcn_advertise(McnHub* hub, int (*echo)(void* parameter));
McnNode_t mcn_subscribe(McnHub* hub, MCN_EVENT_HANDLE event_t, void (*cb)(void* parameter));
fmt_err mcn_unsubscribe(McnHub* hub, McnNode_t node);
fmt_err mcn_publish(McnHub* hub, const void* data);
bool mcn_poll(McnNode_t node_t);
bool mcn_poll_sync(McnNode_t node_t, int32_t timeout);
fmt_err mcn_copy(McnHub* hub, McnNode_t node_t, void* buffer);
fmt_err mcn_copy_from_hub(McnHub* hub, void* buffer);
void mcn_node_clear(McnNode_t node_t);
fmt_err mcn_init(void);
McnList mcn_get_list(void);
```

## 定义消息
定义一条新的 uMCN 消息只需简单几个步骤。这里举例说明定义一条新的消息，消息名称为`my_mcn_topic`，消息内容 (topic) 为：
```c
typedef struct {
	uint32_t a;
	float b;
	int8_t c[4];
} test_data;
```

- 定义消息：在源文件头部（通常为发布该消息的源文件）添加如下定义：
```c
MCN_DEFINE(my_mcn_topic, sizeof(test_data));
```
这里`my_mcn_topic`为消息的名称，`sizeof(test_data)`为消息内容的长度。uMCN对消息的长度和类型没有限制，所以理论上可以用 uMCN 传递任意消息类型。同时uMCN支持一个消息同时有多个发布者 (publisher) 和订阅者 (subscriber)。但是注意，同一个消息 (名称) 不可被重复定义，不然编译时会报重复定义的错误。

- 注册消息：调用`mcn_advertise`函数注册该条消息：
```c
mcn_advertise(MCN_ID(my_mcn_topic), _my_mcn_topic_echo);
```
这里`my_mcn_topic`同样为该消息的名字，`MCN_ID`是一个宏，用来根据消息名称得到`McnHub`节点。`_my_mcn_topic_echo`为该条消息的打印函数。当在控制台输入指令`mcn echo my_mcn_topic`，将调用该条消息的打印函数来打印消息内容。用户可以自定义打印函数来输出想要数据格式。比如`my_mcn_topic`的打印函数如下这样定义：
```c
static int _my_mcn_topic_echo(void* param)
{
	test_data data;
	if(mcn_copy_from_hub((McnHub*)param, &data) == FMT_EOK){
		console_printf("a:%d b:%f c:%c %c %c %c\n", data.a, data.b, data.c[0], data.c[1],               data.c[2], data.c[3]);
	}
	return 0;
}
```

## 订阅消息
订阅者要获取消息的数据，首先需要先订阅消息，通过`mcn_subscribe`函数实现消息的订阅。uMCN支持同步/异步消息订阅，同步方式需要在订阅消息的时候传入一个消息句柄`event_t`用于线程同步。订阅消息遵循以下几个步骤：

- 声明消息：如果在其它文件中（非消息定义的文件中），则需要先申明该条消息。比如对于我们刚刚建立的`my_mcn_topic`，需要在订阅消息的文件中添加
```c
MCN_DECLARE(my_mcn_topic);
```

- 订阅消息：调用`mcn_subscribe(McnHub* hub, MCN_EVENT_HANDLE event_t, void (*cb)(void* parameter))`函数来订阅消息。
其中`cb`为消息发布的回调函数，在每次发布消息时，回调函数将被调用 (注意: 回调函数将在发布消息的线程中被调用)。`event_t`为用于消息同步的事件句柄，这里一般用系统的信号量 (semaphore) 实现。当订阅成功后，函数将返回消息节点句柄`McnNode_t`。

这里分别对同步/异步的消息订阅举例：

**同步订阅**

```c
rt_sem_t event = rt_sem_create("my_event", 0, RT_IPC_FLAG_FIFO);
McnNode_t my_nod = mcn_subscribe(MCN_ID(my_mcn_topic), event, NULL);
```
**异步订阅**

```c
McnNode_t my_nod = mcn_subscribe(MCN_ID(my_mcn_topic), NULL, NULL);
```

## 发布消息
发布消息数据使用`mcn_publish`函数。比如
```c
mcn_publish(MCN_ID(my_mcn_topic), &my_data);
```
这里`my_data`为要发布的数据，其类型为`test_data`。注意这里发布消息的类型需跟``MCN_DEFINE()``定义的消息数据类型一致，否则将发生非预期的后果。

## 读取消息
对于同步/异步的消息订阅方式，读取消息时也分为同步和异步方式。

对于同步订阅方式，调用`mcn_poll_sync`函数查询是否收到新的数据。如果没有新的数据，则将当前线程挂起，直到收到数据或者等待超时返回。当有新的数据到来后，调用`mcn_copy`函数读取数据。例如：
```c
test_data read_data;
if(mcn_poll_sync(my_nod, RT_WAIT_FOREVER)){
	mcn_copy(MCN_ID(my_mcn_topic), my_nod, &read_data);
}
```
这里`my_nod`为消息订阅时返回的消息节点句柄。

如果是采用异步订阅方式，使用`mcn_poll`函数查询是否有新的数据，该函数会立马返回。
```c
test_data read_data;
if(mcn_poll(my_nod){
	mcn_copy(MCN_ID(my_mcn_topic), my_nod, &read_data);
}
```

## uMCN指令
FMT提供了`mcn`的指令来方便查看当前系统中的uMCN消息，指令用法如下：
```shell
msh />mcn --help
usage: mcn [OPTION] ACTION [ARGS]

Action:
 list  List all uMCN topics.
 echo  Echo a uMCN topic.

Option:
 -c, --cnt     Set topic echo count, e.g, -c=10 will echo 10 times.
 -p, --period  Set topic echo period(ms), -p=0 inherits topic period. The default period is 500ms.
```
输入`mcn list`将显示当前系统所有的topic
```shell
msh />mcn list
      Topic       #SUB   Freq(Hz)   Echo
---------------- ------ ---------- ------
usb_status          1      21.7     true
sensor_imu          1      500.0    true
sensor_mag          1      100.0    true
sensor_baro         1      100.0    true
sensor_gps          1      10.0     true
ins_output          3      500.0    true
fms_output          2      125.0    false
control_output      2      250.0    false
pilot_cmd           1       0.0     true
```
其中Topic为消息名称，#SUB为消息订阅者数量，Freq为消息发布频率，Echo表示该条消息是否提供了打印函数。

对于提供了打印函数的消息，可以输入`mcn echo [topic_name]`来打印消息。比如输入`mcn echo sensor_imu`将打印IMU的数据，按任意键结束。
```shell
msh />mcn echo sensor_imu
gyr:-0.000870 -0.002555 -0.003110 acc:0.011333 0.083641 -9.862786
gyr:-0.001118 -0.001791 0.001414 acc:-0.067505 -0.048334 -9.854862
gyr:0.002485 0.005287 0.005572 acc:-0.040842 -0.008587 -9.773908
gyr:-0.000103 -0.001102 0.003385 acc:-0.092554 -0.013108 -9.854832
gyr:-0.001078 0.002592 -0.003151 acc:-0.040336 0.019121 -9.748902
......
```
`mcn echo`指令支持`-c`和`-p`的Option来设置消息打印的**条数**和**频率**，默认打印频率500ms。 如果要打印10条消息，打印频率为100ms，可以输入`mcn echo -c=10 -p=100 sensor_mag`。
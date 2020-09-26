# 参数模块
参数模块 (Param) 提供了系统参数的定义，读取，存储和修改等功能。参数以文件形式存储在存储设备上，如SD卡或者 EEPROM。存储路径默认为`/sys/param.xml`或者`/sys/hil_param.xml` (HIL模式)。
系统上电会自动读取默认路径下的参数文件。若参数文件不存在，则使用默认的参数值。参数也会被记录到 `BLog` 日志头中，供仿真模型读取。目前支持的参数类型包括

```c
enum param_type_t {
	PARAM_TYPE_INT8 = 0,
	PARAM_TYPE_UINT8,
	PARAM_TYPE_INT16,
	PARAM_TYPE_UINT16,
	PARAM_TYPE_INT32,
	PARAM_TYPE_UINT32,
	PARAM_TYPE_FLOAT,
	PARAM_TYPE_DOUBLE,
	PARAM_TYPE_UNKNOWN = 0xFF
};
```
## API
```c
fmt_err param_init(void);
fmt_err param_save(char* path);
fmt_err param_load(char* path);
fmt_err param_set_val(param_t* param, void* val);
fmt_err param_set_val_by_name(char* param_name, void* val);
fmt_err param_set_val_by_full_name(char* group_name, char* param_name, void* val);
fmt_err param_set_string_val(param_t* param, char* val);
fmt_err param_set_string_val_by_name(char* param_name, char* val);
fmt_err param_set_string_val_by_full_name(char* group_name, char* param_name, char* val);
uint32_t param_get_count(void);
int param_get_index(const param_t* param);
param_t* param_get(char* group_name, char* param_name);
param_t* param_get_by_name(const char* param_name);
param_t* param_get_by_full_name(const char* group_name, const char* param_name);
```

## 定义参数

在定义参数之前，有必要先了解一下参数的组织方式。参数以组 (Group) 为单位对参数进行管理，每个组可以包含一到多个参数。

举例说明，定义一个新的 *float* 类型的参数`my_param1`和 *uint32* 类型的参数`my_param2`，它们都属于同一个组`my_group`，则可以按照如下步骤定义：

- 申明Group： 在`param.h`中申明新的组`my_group`
```c
typedef struct {
	......
	param_group_t	PARAM_GROUP(my_group);
} param_list_t;
```

- 定义Group：在`param.c`中定义新的Group

```c
param_list_t param_list = { 
    ......
    PARAM_DEFINE_GROUP(my_group),
};
```

- 申明Parameter：在`param.h`中添加新的参数申明

```c
typedef struct {
	PARAM_DECLARE(my_param1);
	PARAM_DECLARE(my_param2);
} PARAM_GROUP(my_group);
```
- 定义Parameter：在`param.c`中定义新的参数

```c
PARAM_GROUP(my_group) PARAM_DECLARE_GROUP(my_group) = \
{
	PARAM_DEFINE_FLOAT(my_param1, 0.5),
	PARAM_DEFINE_UINT32(my_param2, 1),
};
```
这里定义了两个新的参数`my_param1`（默认值0.5）和`my_param2`（默认值1）。如果是在已有 Group 中添加新的参数，那么则可以省去定义和声明 Group的步骤.

## 读取参数
Param模块提供了如下宏定义来快速获取参数的值 (非查询模式)。用户需要选择匹配参数类型的宏并传入参数的组名称和参数名称. 
```c
#define PARAM_GET_INT8(_group, _name)
#define PARAM_GET_UINT8(_group, _name)
#define PARAM_GET_INT16(_group, _name)
#define PARAM_GET_UINT16(_group, _name)
#define PARAM_GET_INT32(_group, _name)
#define PARAM_GET_UINT32(_group, _name)
#define PARAM_GET_FLOAT(_group, _name)
#define PARAM_GET_DOUBLE(_group, _name)
```

除此之外,还可以通过如下函数来获取参数:
```c
param_t* param_get(char* group_name, char* param_name);
param_t* param_get_by_name(const char* param_name);
param_t* param_get_by_full_name(const char* group_name, const char* param_name);
```
这些函数使用查询的方式查找与`group_name`和`param_name`匹配的参数。当参数较多时,查找速度较慢,所以不建议在算法模块中使用，,仅供系统指令(syscmd)模块使用.

## 设置参数
类似参数读取, param模块也提供了对应的宏来快速设置参数的值:
```c
#define PARAM_SET_INT8(_group, _name, _val)
#define PARAM_SET_UINT8(_group, _name, _val)
#define PARAM_SET_INT16(_group, _name, _val)
#define PARAM_SET_UINT16(_group, _name, _val)
#define PARAM_SET_INT32(_group, _name, _val)
#define PARAM_SET_UINT32(_group, _name, _val)
#define PARAM_SET_FLOAT(_group, _name, _val)
#define PARAM_SET_DOUBLE(_group, _name, _val)
```
同样的,也可以使用如下函数来设置与`group_name`和`param_name`匹配的参数指：
```c
fmt_err param_set_val(param_t* param, void* val);
fmt_err param_set_val_by_name(char* param_name, void* val);
fmt_err param_set_val_by_full_name(char* group_name, char* param_name, void* val);
fmt_err param_set_string_val(param_t* param, char* val);
fmt_err param_set_string_val_by_name(char* param_name, char* val);
fmt_err param_set_string_val_by_full_name(char* group_name, char* param_name, char* val);
```
调用上面的宏/函数设置完参数后，默认不会保存到参数文件，所以当系统重启之后将恢复之前的值。.如需将当前参数保存,需要调用`param_save(char* path)`函数来保存当前修改。其中`path`为参数文件的路径名称，若传入`NULL`则使用默认路径.

## 参数指令
FMT内置了`param`指令来对参数进行操作，其基本用法如下所示:
```shell
msh />param --help
usage: param [OPTION] ACTION [ARGS]

Action:
 list   List group(s) parameters.
 group  List all parameter groups.
 set    Set parameter.
 get    Get parameter.
 save   Save parameters to file.
 load   Load parameters from file.

Option:
 -s, --save  Save parameter value.
```

- *param list*: 显示所有的组和参数:
```shell
msh />param list
SYSTEM:
           BLOG_MODE: 0
CALIB:
          GYRO0_XOFF: 0.000000
          GYRO0_YOFF: 0.000000
          GYRO0_ZOFF: 0.000000
		  ...
```
如果在list后加上组的名称,则只显示对应组的参数:
```shell
msh />param list SYSTEM
SYSTEM:
           BLOG_MODE: 0
           ...
```

- *param group*: 显示所有的组:
```shell
msh />param group
  Parameter Groups
--------------------
SYSTEM
CALIB
INS
FMS
CONTROL
```

- *param set*: 设置参数的值，其用法如下 (方括号表示可选项,尖括号为必填项):
```shell
param set [group] <parameter> <value>
```
比如要将`BLOG_MODE`参数设置为2,可以使用如下指令:
```shell
param set SYSTEM BLOG_MODE 2
```
上述指令也可以简写为`param set BLOG_MODE 2`。但是请注意，由于这里没有指定组名，如果系统中存在两个相同名字的参数,则会默认写入第一个找到的参数.

- *param get*: 获取参数的值, 用法如下：
```shell
param get [group] <parameter>
```

- *param save*: 存储当前参数。可以输入`param save $path`的方式来指定存储路径，否则将存储到默认路径。
- *param load*: 加载参数文件。可以输入`param load $path`的方式来指定加载文件，否则将从默认路径进行加载。
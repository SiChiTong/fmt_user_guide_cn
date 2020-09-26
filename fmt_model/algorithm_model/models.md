# 算法模型库
FMT Model 的算法模型可以分为四类，分别是：

- INS : 惯性导航系统 (Inertial Navigation System) 。通过多传感器融合算法得到被控对象的状态信息（如姿态、速度和位置等）以及各个传感器的健康状况信息等。
- FMS : 飞行管理系统 (Flight Management System) 。主要负责飞行逻辑相关的控制，内部主要由状态机（State Machine）实现。包括飞行模式控制，自动起飞降落，轨迹跟踪控制，安全检测等功能。
- Controller : 控制器模型。基于 FMS 输出的指令信息和 INS 输出的状态信息进行速度环、姿态环和角速度环的控制。控制器的输出经由控制分配（Control Allocation）将控制信号转成作动器（电机）信号。
- Plant : 被控对象模型，如无人机，无人车/船，机器人等。对象模型包括了动力学模型（Dynamic Model），作动器模型（Actuator Model），环境模型（Environment Model）和传感器模型（Sensor Model）。通过对不同的被控对象进行科学的建模，以达到对不同被控对象进行闭环仿真的目的。

目前 FMT Model 包含的算法模型包括：

- INS
	- [Base INS](https://github.com/FirmamentPilot/Base_INS)
	- Template INS	
- FMS
	- [Base FMS](https://github.com/FirmamentPilot/Base_INS)
	- Template FMS
- Controller
	- [Base Controller](https://github.com/FirmamentPilot/Base_Controller)
	- Template FMS
- Plant
	- [Multicopter Model](https://github.com/FirmamentPilot/Multicopter_Model)

在未来会逐步扩充 FMT Model 的算法库模型，以最终达到支持不同的算法和被控对象的目标。它们都使用统一的模型接口标准 ([FMI](../fmt_model_interface/fmt_model_interface.md))，所以不同的算法模块之间可以互相兼容。

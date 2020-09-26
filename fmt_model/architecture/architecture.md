# FMT 模型
FMT Model 为 基于 Matlab/Simulink平台编写的一个完整的仿真模型框架和算法模型库。支持模型在环仿真 (Model-in-the-loop Simulation, MIL)，软件在环仿真 (Software-in-the-loop Simulation, SIL) 和开环仿真 (Open-loop Simulation)。

FMT Model 的模型库包括一套完整的无人机算法模型。可以分为导航系统（INS），飞行管理系统（FMS） ，控制系统（Controller） 和 被控对象建模（Plant）四个部分。每个部分可以包含多个算法模型，以支持不同的算法和被控对象。可以根据需要进行自由组合，只要模型符合 FMT Model Interface (FMI) 模型接口规范。 算法模型可以直接编译生成 C/C++ 源码并无缝嵌入到 FMT 嵌入式飞控系统中。

## FMT Model 架构
FMT Model 为包含飞控的算法模型已经仿真框架，其架构如下图所示。

![model_structure](figures/fmt_model.png)

FMT Model 包含了仿真框架 (MIL, SIL, HIL 和 Open-loop Simulation等)，可以基于仿真框架进行被控对象的仿真。仿真框架里面的模型来自算法模型库 (Algorithm Model)，而算法模型库通过 submodule 的方式链接到外部的算法模型。submodule 用来独立维护具体的算法模型，用户可以添加新的模型然后链接到算法模型库中，从而可以被 FMT_Model所使用。

Model Library 为模型库，里面包括滤波器模型库，四元数模型库，基础数学算法库等。Utils和Scripts包含了实用工具的脚本，如传感器校准脚本，日志解析脚本等。

FMT Model 算法模型库主要由四类算法模型组成，分别是：

- INS : 惯性导航系统 (Inertial Navigation System) 。通过多传感器融合算法得到被控对象的状态信息（如姿态、速度和位置等）以及各个传感器的健康状况信息等。
- FMS : 飞行管理系统 (Flight Management System) 。主要负责飞行逻辑相关的控制，内部主要由状态机（State Machine）实现。包括飞行模式控制，自动起飞降落，轨迹跟踪控制，安全检测等功能。
- Controller : 控制器模型。基于 FMS 输出的指令信息和 INS 输出的状态信息进行速度环、姿态环和角速度环的控制。控制器的输出经由控制分配（Control Allocation）将控制信号转成作动器（电机）信号。
- Plant : 被控对象模型，如无人机，无人车/船，机器人等。对象模型包括了动力学模型（Dynamic Model），作动器模型（Actuator Model），环境模型（Environment Model）和传感器模型（Sensor Model）。通过对不同的被控对象进行科学的建模，以达到对不同被控对象进行闭环仿真的目的。
# FMT 模型
FMT Model 为 基于 Matlab/Simulink平台编写的一个完整的仿真模型框架和算法模型库。支持模型在环仿真 (Model-in-the-loop Simulation, MIL)，软件在环仿真 (Software-in-the-loop Simulation, SIL) 和开环仿真 (Open-loop Simulation)。

FMT Model 的模型库包括一套完整的无人机算法模型。可以分为导航系统（INS），飞行管理系统（FMS） ，控制系统（Controller） 和 被控对象建模（Plant）四个部分。每个部分可以包含多个算法模型，以支持不同的算法和被控对象。可以根据需要进行自由组合，只要模型符合 FMT Model Interface (FMI) 模型接口规范。 算法模型可以直接编译生成 C/C++ 源码并无缝嵌入到 FMT 嵌入式飞控系统中。

## FMT Model 架构
FMT Model 为包含飞控的算法模型已经仿真框架，其架构如下图所示。

![model_structure](figures/fmt_model.png)

FMT Model 包含了仿真框架 (MIL, SIL, HIL 和 Open-loop Simulation等)，可以基于仿真框架进行被控对象的仿真。仿真框架里面的模型来自算法模型库 (Algorithm Model)，而算法模型库通过 submodule 的方式链接到外部的算法模型。submodule 用来独立维护具体的算法模型，用户可以添加新的模型然后链接到算法模型库中，从而可以被 FMT_Model所使用。

Model Library 为模型库，里面包括滤波器模型库，四元数模型库，基础数学算法库等。Utils和Scripts包含了实用工具的脚本，如传感器校准脚本，日志解析脚本等。
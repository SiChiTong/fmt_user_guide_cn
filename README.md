# Firmament Autopilot

## 概述
Firmament (FMT) Autopilot 是首个基于模型设计 (Model-Based Design， 简称MBD) 的开源自驾仪。FMT 项目从 Starry Pilot 开源飞控发展而来。FMT 包括嵌入式一个主要用C语言写的嵌入式飞控系统以及基于 Matlab/Simulink 开发的算法模型库和仿真框架。

基于FMT平台可以更快速的开发和验证的飞控算法，无需手动编写嵌入式代码，只需要在Simulink中通过图形化的方式设计算法模型并一键生成代码并合入飞控。

**项目源码地址**：https://github.com/FirmamentPilot

## 为什么使用 FMT
基于模型设计是一种数学及可视化的设计方法，通过图形化的方式设计复杂的飞控或者其它控制系统。目前大部分开源飞控系统的研发手段还是采用传统的手动编码的开发模式。这种方式固然有其优势，但是其劣势也越来越明显。特别当系统变得越来越庞大，功能越来越复杂，使用手写的算法模块变得越来越难以维护，也不可避免的造成代码的安全性和可移植性越来越差。

FMT 的核心算法使用 Simulink 搭建，继承了 MBD 开发模式的诸多优点。基于 FMT 可以更快地开发出高性能、高可靠的自驾仪系统。总结起来，MBD 具有以下优势：

1. 极大提升算法开发效率，节省时间和人力成本。
2. 减少手动编写代码过程中产生的错误，提升系统稳定性。
3. 极大提升算法的优化和 Debug 效率，简化系统测试和验证流程。
4. 提高算法的可维护性和可移植性。

FMT 包括一个轻量级，高实时性 ( 时间误差 < 1us ) 和高性能 ( 更低的 CPU 使用率 ) 的嵌入式飞控系统 FMT_Firmware，支持当前最流行的开源飞控硬件 Pixhawk V2。

- C 语言编写的轻量级飞控系统，高度模块化设计，更加易于使用和二次开发。
- 基于硬实时操作系统 RT-Thread，时间误差 < 1us。
- 高度优化的系统结构和算法，可以用更高频率运行复杂的算法模块。比如以 500Hz 运行基础导航和控制器，250Hz 运行飞行管理状态机的 CPU 使用率 < 30%，开启日志功能 CPU 使用率 < 50%。
- 支持 Mavlink V1.0/V2.0
- 支持半物理硬件在环仿真 (Hardware-in-the-loop Simulation, HIL) 和无人机动力学模型直接运行在飞控上的硬件仿真 (Simulator-in-hardware, SIH)。

FMT 还包括了一个完整的仿真模型框架，支持模型在环仿真 (Model-in-the-loop Simulation, MIL)，软件在环仿真 (Software-in-the-loop Simulation, SIL) 和 开环仿真 (Open-loop Simulation)。 以及一套完整的多旋翼基础算法模型，包括：

- 基础导航模型：基于互补滤波算法 (Complementary Filter) 的通用导航模型，支持的传感器包括： IMU，磁力计，气压计，GPS，超声测距仪和光流传感器（可扩展其它传感器的融合）。导航输出的状态信息包括：姿态，速度，世界坐标位置 （经纬高），相对位置，对地高度，陀螺零偏，加速度零偏，传感器健康状态，导航系统状态等信息。适用于大部分的被控对象（无人机，无人车/船，机器人等）和应用场景。
- 基础飞行管理系统模型：包括飞行模式控制（轨迹飞行，定点，定高，手动，特技模式等），解锁/上锁逻辑，自动起飞降落，安全检查等逻辑控制。
- 基础控制模型：基于串级 PID 的控制器模型和控制分配。包括速度环、角度环和角速度环控制器。
- 多旋翼的对象模型：包括动力学模型，作动器模型，环境模型和传感器模型等。

各个模型可以一键生成 C/C++ 代码，并无缝合入 FMT 嵌入式飞控。FMT_Model 除了提供一套基础的算法模型以外，还为各个模型提供了模板，您可以基于模板快速开发自己的算法模型并部署到飞控上。

# 手册目录

## FMT Firmware

- [FMT Firmware架构](fmt_firmware/architecture/architecture.md)
- [快速上手](fmt_firmware/quick_start/quick_start.md)
- [组件 (Module)](fmt_firmware/module/module.md)
- [驱动和硬件虚拟设备 (Driver/HAL)](fmt_firmware/device/device.md)
- 任务 (Task)
- 硬件在环仿真 (HIL/SIH)

## FMT Model

- FMT Model架构
- 快速上手
- 模型在环仿真 (MIL Simulation)
- 开环仿真 (Open-loop Simulation)
- 模型接口标准 (FMT Model Interface)
- 算法模型库
- 代码生成与模型合入
- 模型二次开发

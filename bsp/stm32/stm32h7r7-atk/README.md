# STM32H7r7-atk 开发板 BSP 说明

## 简介

本文档为 wenshan 为 STM32H7r7-atk开发板提供的 BSP (板级支持包) 说明。

主要内容如下：

- 开发板资源介绍
- BSP 快速上手
- 进阶使用方法

通过阅读快速上手章节开发者可以快速地上手该 BSP，将 RT-Thread 运行在开发板上。在进阶使用指南章节，将会介绍更多高级功能，帮助开发者利用 RT-Thread 驱动更多板载资源。

## 开发板介绍

阿波罗 STM32H7r7 是正点原子推出的一款基于 ARM Cortex-M7 内核的开发板，最高主频为 600Mhz，该开发板具有丰富的板载资源，可以充分发挥 STM32H7r7 的芯片性能。

开发板外观如下图所示：

![board](figures/board.jpg)

该开发板常用 **板载资源** 如下：

- MCU：STM32H7r7l8h，主频 600MHz，128K FLASH ，620K RAM
- 外部 HyperRam：W955K8MBYA，32MB
- 外部 SD NAND：MKDV4GCL，2GB
- 外部 HyperFlash：GD25LX256MEBFR，32MB
- 常用外设
    - LED：2个，DS0（红色，PB1），DS1（绿色，PB0）
    - 按键：4个，KEY_UP（兼具唤醒功能，PA0），K0（PH3），K1（PH2），K2（PC13）
- 常用接口：USB 转串口、SD 卡接口、以太网接口、LCD 接口等
- 调试接口，标准 SWD

开发板更多详细信息请参考正点原子 [STM32阿波罗开发板介绍](https://eboard.taobao.com/index.htm)。

## 外设支持

本 BSP 目前对外设的支持情况如下：

| **板载外设**      | **支持情况** | **备注**                                                |
| :---------------- | :----------: | :------------------------------------------------------ |
| USB 转串口        |     支持     |                                                 |
| ESP32接口              |     支持     |                                                        |
| hyperbus Flash        |     支持     |                                                         |
| 以太网            |     支持     |                                                         |
| SD卡              |     支持     |                                                         |
| HyperRAM             |     支持     |                                                         |
| **片上外设**      | **支持情况** | **备注**                                                |
| GPIO              |     支持     |                                                         |
| UART              |     支持     |                                                         |
| SPI               |     支持     |                                                         |
| I2C               |     支持     | 软件iic                                                 |
| ETH            |   支持   |                                                         |
| CAN-FD            |   支持   | HDR暂不支持                                                        |

## 使用说明

使用说明分为如下两个章节：

- 快速上手

    本章节是为刚接触 RT-Thread 的新手准备的使用说明，遵循简单的步骤即可将 RT-Thread 操作系统运行在该开发板上，看到实验效果 。

- 进阶使用

    本章节是为需要在 RT-Thread 操作系统上使用更多开发板资源的开发者准备的。通过使用 ENV 工具对 BSP 进行配置，可以开启更多板载资源，实现更多高级功能。


### 快速上手

本 BSP 为开发者提供 MDK4、MDK5 和 IAR 工程，并且支持 GCC 开发环境。下面以 MDK5 开发环境为例，介绍如何将系统运行起来。

**请注意！！！**

在执行编译工作前请先打开ENV执行以下指令（该指令用于拉取必要的HAL库及CMSIS库，否则无法通过编译）：

```bash
pkgs --update
```

#### 硬件连接

使用数据线连接开发板到 PC，打开电源开关。

#### 编译下载

双击 project.uvprojx 文件，打开 MDK5 工程，编译并下载程序到开发板。

> 工程默认配置使用 DAP_LINK 仿真器下载程序，在通过 DAP_LINK 连接开发板的基础上，点击下载按钮即可下载程序到开发板

#### 运行结果

下载程序成功之后，系统会自动运行，LED闪烁。

连接开发板对应串口到 PC , 在终端工具里打开相应的串口（115200-8-1-N），复位设备后，可以看到 RT-Thread 的输出信息:

```bash
 \ | /
- RT -     Thread Operating System
 / | \     5.1.0 build Jun  2 2025 12:43:54
 2006 - 2024 Copyright by RT-Thread team
```

### 进阶使用

此 BSP 默认只开启了 GPIO 和 串口1 的功能，如果需使用更多高级功能，需要利用 ENV 工具对BSP 进行配置，步骤如下：

1. 在 bsp 下打开 env 工具。

2. 输入`menuconfig`命令配置工程，配置好之后保存退出。

3. 输入`pkgs --update`命令更新软件包。

4. 输入`scons --target=mdk4/mdk5/iar` 命令重新生成工程。

本章节更多详细的介绍请参考 [STM32 系列 BSP 外设驱动使用教程](../docs/STM32系列BSP外设驱动使用教程.md)。

## 注意事项

1. UART暂未完成对DMA的支持，同时开启uart7和uart1的dma会进入hardfault，只打开uart1的dma会有概率进入hardfault。
2. 开发板连接正点原子的esp32模块可能会报execute command (AT+CIPDNS?) failed错误，这是因为esp32的at固件不支持这个at指令，需要升级esp32的固件版本，详情参照网上教程。
3. 正点原子暂未提供可以用在rtthread studio上的stldr下载文件，故在rtthread studio上只能编译还不能下载，现在只有在keil上用的flm下载文件。
4. 如果使用eth的话，hal库一定要是1.3版本以上！
5. 核心板有四种，这里使用的是正点原子的h7r7的高配板。
6. 需要先下载对应的bootloader，在[Wenshan-736/stm32h7r7-atk](https://github.com/Wenshan-736/stm32h7r7-atk/tree/main)里面有工程，文件atk-bootloader.bin是编译后的二进制文件

## 联系人信息

维护人:[Wenshan-736](https://github.com/Wenshan-736), 邮箱：<736810738@qq.com>

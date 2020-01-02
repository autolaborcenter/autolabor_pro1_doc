# 手柄控制

使用手柄控制机器人运动

<iframe frameborder="0" width="640" height="360" src="/lib/video/docplay/8" allowfullscreen></iframe>

更多详情，请查看[此处](/usedoc/autolaborPro1/use#手柄控制)。

# 串口控制

Autolabor Pro1可以使用手柄控制和上位机控制两种方式来控制，在上位机模式下，用户可以选择使用串口调试助手向AP1发送指令，或使用ROS驱动包与AP1通信。

以Windows串口调试工具为例，介绍Autolabor Pro1 串口指令发送方法，同时可作为检测AP1串口是否正常的方法。

## 准备工作：

* Autolabor Pro1
* 串口数据线
* 上位机（Windows系统）

## 操作步骤

1. 下载串口调试工具
任一串口工具都可，推荐serial port utility（[下载](https://serialport.en.softonic.com/)）
2. 将AP1架起来，四轮悬空
<img src="/res/img/pro/2/tab-4-1.png" width="50%"/>

3. 打开电源开关，切换至上位机模式，打开急停开关
<img src="/res/img/pro/2/tab-4-2.png" width="50%"/>
4. 使用串口数据线，连接上位机
注：请将插在车上的数据线一端拧紧
上位机操作环境如是Win10系统，连接后会自动安装设备驱动。
若无法自动安装，请使用请使用附赠光盘/[下载驱动](http://download.autolabor.com.cn/firmware/PL2303_Prolific_DriverInstaller_v1200.zip)进行手动安装。
5. 安装完毕后，打开设备管理器，查看端口号
![在这里插入图片描述](/res/img/pro/2/tab-4-3.png)
6. 打开串口工具，进行配置
波特率：115200
接收发送: Hex格式
7. 打开串口，发送如下指令
前进：
```
55 AA 02 02 05 00 FA 55 AA 09 00 01 00 04 00 04 00 00 00 00 F7
```
* [指令/协议规则介绍](/usedoc/autolaborPro1/protocolRule/)
* [指令示例](/usedoc/autolaborPro1/controlRule/)

如以上操作无误，AP1与串口数据线均正常，AP1四轮应同时向前运动。若无运动，请参见下方常见问题。


### 常见问题

1. 发送串口指令，AP1无任何反应
请检查是否处于上位机模式，急停开关是否打开（右转打开）
请检查插在AP1上的串口线是否插紧
2. 发送指令，串口工具没有返回任何数据
请检查串口工具发送接收设置，是否是Hex/16进制格式
请检查命令是否发送正确
3. 若以上操作均无问题，请进行串口数据线短接测试
说明：此测试只是测试串口数据线，和AP1无关。
	1. 使用导线，将数据线2和3连接在一起（短接）
![在这里插入图片描述](/res/img/pro/2/tab-5-1.jpg)
![在这里插入图片描述](/res/img/pro/2/tab-5-2.jpg)
	2. 将USB端插在上位机上，另一端不插
	3. 打开串口工具
	4. 勾选掉【显示发送】（发送的指令不再显示在数据区域内）
	5. 发送串口指令

依照以上操作，如无任何回复，属串口数据线质量问题，请截图保存测试结果联系微信客户服务平台处理。

# ROS键盘控制

## 准备工作：

* Autolabor Pro1
* 串口数据线
* 上位机（ubuntu16.04+ROS Kinetic/AutolaborOS amd64 系统）
* 键盘

## 操作步骤

1. 下载ROS驱动、ROS键盘控制驱动，安装编译

<iframe frameborder="0" width="640" height="360" src="/lib/video/docplay/10" allowfullscreen></iframe>

2. 查找机器人串口，设置权限

<iframe frameborder="0" width="640" height="360" src="/lib/video/docplay/11" allowfullscreen></iframe>

3. 控制机器人运动

<iframe frameborder="0" width="640" height="360" src="/lib/video/docplay/12" allowfullscreen></iframe>


# 支架安装

<iframe frameborder="0" width="640" height="360" src="/lib/video/docplay/7" allowfullscreen></iframe>





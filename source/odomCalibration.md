# ##里程计标定

里程计标定也叫里程计校准，即在当前运行环境下重新计算运动模型，得到里程计的运动模型参数，此教程**只适用于使用ROS控制AP1机器人**的用户。

Autolabor Pro1 出厂时已做过标定了，在一般运行环境下（地毯、水泥、普通瓷砖等平坦路面）不用重新做标定，但如果您的运行环境是非一般环境，机器人可能就需要重新做标定，比如【经过打磨过的】并且还有【镜面效果】的水泥路面，或摩擦力较大路面，如果您在使用导航套件建图时效果不佳，也可以进行标定。

对于不太确定产品是否需要标定的用户，可先进行里程计测试，根据测试结果来判断机器人是否需要标定。

准备工作：

* Autolabor Pro1
* 上位机 （Ubuntu16.04 + ROS Kinetic 或 AutolaborOS)
* 所需软件 ([下载](http://www.autolabor.com.cn/download) /如使用AutolaborOS则无需下载)
    * Autolabor Pro1 ROS驱动包
    * Autolabor ROS键盘控制包

注：如使用AutolaborOS则无需下载，AutolaborOS-2.1.4及之后的版本，可以点击桌面的键盘控制

## 检测机器人是否需要标定



预备知识：

未使用AutolaborOS的用户要求：

学习使用ROS键盘控制Autolabor Pro1（[控制教程](http://www.autolabor.com.cn/usedoc/autolaborPro1/getStart#ROS%E9%94%AE%E7%9B%98%E6%8E%A7%E5%88%B6)）

使用AutolaborOS的用户可直接点击桌面的键盘控制图标，如无该图标可点击【开始导航】。


### 操作步骤：

1. 启动键盘控制，使用ROS键盘控制AP1原地转360度
2. 打开一个新的terminal，运行
  ```
  $ rviz
  ```
3. rviz窗口打开后，将fixed frame选择为odom
4. 关闭其他所有勾选，只保留grid和tf（如没有grid，左下角add新增）
5. 打开tf，下拉出来frames的内容，关闭其他所有勾选，保留baselink和odom
6. rviz右侧界面可看两个重合的坐标系(baselink和odom)
7. 对机器人（实体）的四个轮子做标记，标记此时车的位置
8. 键盘控制机器人原地360度转一圈（请必须记住此时旋转的方向，标定会用到），控制机器人回到刚刚标记的位置（重合），保持机器人与标记的初始位置方向一致
9. 观察rviz中的2个坐标系是否重合

如果不重合，表示需要标定。

如果基本重合，表示不需要标定。

## 对机器人进行里程计标定

### 操作步骤：

1. 打开一个新的terminal
2. 运行
  ```
  $ rosrun tf tf_echo /odom /base_link
  ```
3. 在出现的数据中查找in RPY (degree)[0,0,X]
4. 查看X值，如果刚刚键盘控制车时是顺时针转，用360-X，如果是逆时针转，用360+X，得到Y
5. 计算 model_param*Y/360，得到计算结果
6. 打开启动的launch文件，找到Autolabor Pro1驱动部分（autolabor_pro1_driver），找到model_param参数，将model_param改为上一步得到的结果
7. 保存、关闭launch文件
8. 关闭terminal中的运行的launch(ctrl+c)，如果rviz关闭时弹出窗口，询问是否需要保存，点击【without saving】（不保存）
9. 再次重复【检测机器人是否需要标定】的操作

如果不重合，表示需要标定，如果基本重合，表示不需要标定，重复以上【标定】工作，一次次的进行，直到基本重合。

注意：除第一次标定时使用的model_param为驱动中的原始值，之后每一次的标定操作中model_param为上一次标定计算的model_param结果（第5步）


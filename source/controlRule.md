### <mark>注意</mark>：
* 在使用串口发送指令时，底层超过 **1s** 没有接收到上位机的指令，会向上位机返回错误状态码FF (error状态:连接超时），此时用户如果想要重新控制车辆，需要发送Reset命令
* 在调试发送指令时，先发一条Reset命令，然后接着发指令，如:
	
		0x 55 AA 02 02 05 00 FA
		0x 55 AA 09 00 01 00 04 00 00 00 00 00 00 F3
		0x 55 AA 02 02 05 00 FA
		0x 55 AA 09 00 01 00 04 00 00 00 00 00 00 F3
		...

#### 串口测试速度指令样例

##### 左转

	55 AA 02 02 05 00 FA 55 AA 09 00 01 00 00 00 04 00 00 00 00 F3
	
##### 右转

	55 AA 02 02 05 00 FA 55 AA 09 00 01 00 04 00 00 00 00 00 00 F3

##### 前进

	55 AA 02 02 05 00 FA 55 AA 09 00 01 00 04 00 04 00 00 00 00 F7

##### 后退

	55 AA 02 02 05 00 FA 55 AA 09 00 01 FF FA FF FA 00 00 00 00 F7

下面列出几个常用的控制指令示例：

# 控制车轮速度指令
假设请求左车轮速度为0.1m/s，传输数据帧内容为：

	0x 55 AA 09 00 01 00 04 00 00 00 00 00 00 F3
<table>
	<tbody>
		<tr align="center">
			<td rowspan="2">Header</td>
			<td rowspan="2">Length</td>
			<td rowspan="2">Sequence</td>
			<td colspan="5">Payload</td>
			<td rowspan="2">Checksum </td>
		</tr>
		<tr align="center">
			<td width="10%">MsgID</td>
			<td colspan="4">PARAM</td>
		</tr>
		<tr align="center">
			<td>55 AA</td>
			<td>09</td>
			<td>00</td>
			<td>01</td>
			<td>00 04</td>
			<td>00 00</td>
			<td>00 00</td>
			<td>00 00</td>
			<td>F3</td>
		</tr>
		<tr align="center">
			<td>起始标志位</td>
			<td>有效数据的长度为9个字节</td>
			<td>第一个指令</td>
			<td>控制车轮速度指令</td>
			<td>左车轮</td>
			<td>右车轮</td>
			<td colspan="2">固定不变为0</td>
			<td>异或校验码</td>
		</tr>
	</tbody>
</table>

如上文所说，通常我们所说的速度是以m/s为单位的速度，而指令中车轮速度的参数实际上是单位时间内期望编码器的计数，在此需要将指令的速度V（m/s）结合AP1下位机的物理参数进行计算，下面是车轮速度换算方法。

AP1下位机物理参数如下表：

名称  | 参数 | 说明
:-------------: | :-------------: | :-------------
wheel_diameter |  0.25 | 车轮直径，单位：米
encoder_resolution | 1600 | 编码器转一圈产生的脉冲数
PID_RATE  |  50 | PID调节PWM值的频率

假设我们给AP1左轮V=0.1m/s的速度（右轮速度的计算指令与此相同），<a name="caculate-method">计算方法</a>如下：  

车轮转动一圈，移动的距离为轮子的周长：  
	
	distance_one_round
	=wheel_diameter*π
	=wheel_diameter*3.1415926
	=0.25*3.1415926
	
车轮转动一圈，编码器产生的脉冲数为：
	
	wheel_encoder_resolution
	=1*encoder_resolution
	=1*1600
	=1600
	
注：编码器与车轮连接在一起，车轮转1圈，编码器转1圈；编码器的脉冲数可以理解为编码器计数，编码器自转一圈计数1600，则车轮转一圈编码器计数为1600。
	
所以AP1每移动1米产生脉冲数（编码器的计数）为:	
	
	ticks_per_meter
	=(1/distance_one_round)*wheel_encoder_resolution
	=1/(0.25*3.1415926)*1600
	=2037.18
	
又因为PID的频率是1秒钟50次，所以指令的参数计算方法为:
	
	int(V*ticks_per_meter/PID_RATE)
	=int(0.1*2037.18/50)
	=4
	
	
用户可直接使用0.1m/s速度的计算结果来对应自己期望的速度换算成相应的数值，如0.2m/s为8，1m/s为40等。
	
接着将计算出来的4换算为16进制为：
	
	00 04

则速度指令的PARAM为 

	00 04 00 00 00 00 00 00 
	

注：如果用户发送的速度超过了最大速度，则会按最大速度行进。

# 获取电池电量指令	
	0x 55 AA 02 01 02 00 FE
<table>
	<tbody>
		<tr align="center">
			<td rowspan="2">Header</td>
			<td rowspan="2">Length</td>
			<td rowspan="2">Sequence</td>
			<td colspan="2">Payload</td>
			<td rowspan="2">Checksum </td>
		</tr>
		<tr align="center">
			<td>MsgID</td>
			<td>PARAM</td>
		</tr>
		<tr align="center">
			<td>55 AA</td>
			<td>02</td>
			<td>01</td>
			<td>02</td>
			<td>00</td>
			<td>FE</td>
		</tr>
		<tr align="center">
			<td>起始标志位</td>
			<td>有效数据的长度为2个字节</td>
			<td>第二个指令</td>
			<td>小车的电量请求</td>
			<td>参数为00</td>
			<td>异或校验码</td>
		</tr>
	</tbody>
</table>

# 重置状态指令	
当接收到下位机发送的错误状态码FF时，需要将AP1状态重置（Reset)，才能重新恢复控制，指令如下：  
	
	0x 55 AA 02 02 05 00 FA
<table>
	<tbody>
		<tr align="center">
			<td rowspan="2">Header</td>
			<td rowspan="2">Length</td>
			<td rowspan="2">Sequence</td>
			<td colspan="2">Payload</td>
			<td rowspan="2">Checksum </td>
		</tr>
		<tr align="center">
			<td>MsgID</td>
			<td>PARAM</td>
		</tr>
		<tr align="center">
			<td>55 AA</td>
			<td>02</td>
			<td>02</td>
			<td>05</td>
			<td>00</td>
			<td>FA</td>
		</tr>
		<tr align="center">
			<td>起始标志位</td>
			<td>有效数据的长度为2个字节</td>
			<td>第三个指令</td>
			<td>重置状态指令</td>
			<td>参数为00</td>
			<td>异或校验码</td>
		</tr>
	</tbody>
</table>	

# 清除编码器计数指令	
 
	
	0x 55 AA 02 03 06 00 F8
<table>
	<tbody>
		<tr align="center">
			<td rowspan="2">Header</td>
			<td rowspan="2">Length</td>
			<td rowspan="2">Sequence</td>
			<td colspan="2">Payload</td>
			<td rowspan="2">Checksum </td>
		</tr>
		<tr align="center">
			<td>MsgID</td>
			<td>PARAM</td>
		</tr>
		<tr align="center">
			<td>55 AA</td>
			<td>02</td>
			<td>03</td>
			<td>06</td>
			<td>00</td>
			<td>F8</td>
		</tr>
		<tr align="center">
			<td>起始标志位</td>
			<td>有效数据的长度为2个字节</td>
			<td>第四个指令</td>
			<td>请求清除编码器计数</td>
			<td>参数为00</td>
			<td>异或校验码</td>
		</tr>
	</tbody>
</table>

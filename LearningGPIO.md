<a name="content">目录</a>

[树莓派GPIO入门](#title)
- [前言：GPIO引脚说明](#introduction-to-gpio)
- [01-使用GPIO接口控制发光二极管闪烁](#01-shrink-led)
- [02-GPIO控制LED亮度，制作呼吸灯效果](#02-control-led-brightness)




<h1 name="title">树莓派GPIO入门</h1>

<a name="introduction-to-gpio"><h2>前言：GPIO引脚说明 [<sup>目录</sup>](#content)</h2></a>

<p align="center"><img src=./picture/LearningGPIO-introduction-GPIO-1.jpg width=600 /></p>

可以在命令行执行`gpio readall`查看各个引脚的说明

<p align="center"><img src=./picture/LearningGPIO-introduction-GPIO-2.png width=600 /></p>

<a name="01-shrink-led"><h2>01-使用GPIO接口控制发光二极管闪烁 [<sup>目录</sup>](#content)</h2></a>

<table>
<tbody>
<tr>
	<td>最终效果：</td>
	<td>所需材料</td>
</tr>
<tr>
	<td><p align="center"><img src=./picture/LearningGPIO-01-1.gif width=300 /></p></td>
	<td><p align="center"><img src=./picture/LearningGPIO-01-2.jpg width=300 /></p></td>
</tr>
</tbody>
</table>

**原理说明：**

LED灯有一长一短两根针脚，如果将较长的一根连上电源正极，较短的一根脸上电源负极造成电位差就可以点亮LED灯。

但如果两个针脚同时都是负极（低电平）或者都是正极（高电平）则不会产生电位差也就不会被点亮。
将较短的一根连上树莓派的GND（也就是负极）端，较长的一根不要直接连上树莓派的5V或者3.3V（两者都可理解为正极或高电平，以后统称高低电平，不再另行解释），而是连接到一个GPIO针脚上。

然后我们可以通过程序控制GPIO口的电位高低状态即可控制LED的亮（GPIO口设置为高电平）或灭（GPIO口设置为低电平）。

<p align="center"><img src=./picture/LearningGPIO-01-3.png width=300 /></p>

**Python代码：**

```
#!/usr/bin/env python
# encoding: utf-8

import RPi.GPIO
import time

# 指定GPIO口的选定模式为GPIO引脚编号模式（而非主板编号模式）
RPi.GPIO.setmode(RPi.GPIO.BCM)

# 指定GPIO14（就是LED长针连接的GPIO针脚）的模式为输出模式
# 如果上面GPIO口的选定模式指定为主板模式的话，这里就应该指定8号而不是14号。
RPi.GPIO.setup(14, RPi.GPIO.OUT)

# 循环10次
for i in range(0, 10):
    # 让GPIO14输出高电平（LED灯亮）
    RPi.GPIO.output(14, True)
    # 持续一段时间
    time.sleep(0.5)
    # 让GPIO14输出低电平（LED灯灭）
    RPi.GPIO.output(14, False)
    # 持续一段时间
    time.sleep(0.5)

# 最后清理GPIO口（不做也可以，建议每次程序结束时清理一下，好习惯）
RPi.GPIO.cleanup()
```

<a name="02-control-led-brightness"><h2>02-GPIO控制LED亮度，制作呼吸灯效果 [<sup>目录</sup>](#content)</h2></a>











---

参考资料：

(1) [个人博客网站：芒果爱吃胡萝卜](http://blog.mangolovecarrot.net/)

(2) [CSDN：树莓派学习二（点亮LED灯）](https://blog.csdn.net/qq_38005186/article/details/73076067)

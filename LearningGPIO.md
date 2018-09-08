<a name="content">目录</a>

[树莓派GPIO入门](#title)
- [前言：GPIO引脚说明](#introduction-to-gpio)
- [01-使用GPIO接口控制发光二极管闪烁](#01-shrink-led)
- [02-GPIO控制LED亮度，制作呼吸灯效果](#02-control-led-brightness)
- [03-GPIO控制RGB彩色LED灯](#03-control-rgb-led)



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

<p align="center"><img src=./picture/LearningGPIO-02-1.gif width=500 /></p>

**原理说明：**

我们知道，通过LED的电流越大，LED越亮，电流越小，LED越暗。如果可以控制输出电流大小就可以控制LED的明暗了。但是树莓派的各引脚并没有直接调整输出电流大小的功能。要想别的办法。

我们先学习一个名词：**脉宽调制（PWM）**。简单的说，PWM技术就是不停的通断电路并控制通断持续的时间片段长度来控制用电器在单位时间内实际得到的电能。这么说好像还是复杂了，再简单点说，如果你的手足够快，打开电灯开关后马上关闭，如果这个时间间隔足够短，灯丝还没有全部亮起来就暗下去了。你再次打开电灯再关闭，再打开再关闭。。。如果你一直保持相同的频率，那么电灯应该会保持一个固定的亮度不变。理论上，你可以通过调整开灯持续的时间长度和关灯持续的时间长度的比例就能得到不同亮度了。这个比例被称为“**占空比**”。PWM就是差不多这个意思。

树莓派1代B型的26个针脚里，有一个特殊的GPIO口是支持硬件PWM的，不过从B+开始不知道什么原因这个很实用的接口被去掉了。但是没关系，根据我上面的描述，我们完全可以自己写一个程序来模拟PWM。不想自己写，没关系，我们强大的GPIO库已经帮我们写好了，直接用就可以了。

硬件的连接方式与 01 中的一模一样

**Python代码：**

```
#!/usr/bin/env python
# encoding: utf-8

import RPi.GPIO
import time

RPi.GPIO.setmode(RPi.GPIO.BCM)
RPi.GPIO.setup(14, RPi.GPIO.OUT)

# 创建一个 PWM 实例，需要两个参数
## 第一个是GPIO端口号，这里我们用14号
## 第二个是频率（Hz），频率越高LED看上去越不会闪烁，相应对CPU要求就越高，设置合适的值就可以
pwm = RPi.GPIO.PWM(14, 80)

# 启用 PWM，参数是占空比，范围：[0.0, 100.0]
pwm.start(0)

try:
    while True:
        # 电流从小到大，LED由暗到亮
        for i in xrange(0, 101, 1):
            # 更改占空比，
            pwm.ChangeDutyCycle(i)
            time.sleep(.02)
            
        # 再让电流从大到小，LED由亮变暗
        for i in xrange(100, -1, -1):
            pwm.ChangeDutyCycle(i)
            time.sleep(.02)

# 最后一段是一个小技巧。这个程序如果不强制停止会不停地执行下去。
# 而Ctrl+C强制终端程序的话，GPIO口又没有机会清理。
# 加上一个try except 可以捕捉到Ctrl+C强制中断的动作，
# 试图强制中断时，程序不会马上停止而是会先跳到这里来做一些你想做完的事情，比如清理GPIO口。
except KeyboardInterrupt:
    pass

# 停用 PWM
pwm.stop()

# 清理GPIO口
RPi.GPIO.cleanup()
```

代码总结：

> - 通过设置较高的频率使LED灯看起来不闪烁
> - 通过调整占空比，调整LED灯的亮度，占空比越大说明：开灯持续的时间长度和关灯持续的时间长度的比例越大，LED灯越亮，反之越暗

<a name="03-control-rgb-led"><h2>03-GPIO控制RGB彩色LED灯 [<sup>目录</sup>](#content)</h2></a>

<table>
<tbody>
<tr>
	<td>最终效果：</td>
	<td>所需材料</td>
</tr>
<tr>
	<td><p align="center"><img src=./picture/LearningGPIO-03-1.gif height=700 /></p></td>
	<td><p align="center"><img src=./picture/LearningGPIO-03-2.jpg height=700 /></p></td>
</tr>
</tbody>
</table>







---

参考资料：

(1) [个人博客网站：芒果爱吃胡萝卜](http://blog.mangolovecarrot.net/)

(2) [CSDN：树莓派学习二（点亮LED灯）](https://blog.csdn.net/qq_38005186/article/details/73076067)

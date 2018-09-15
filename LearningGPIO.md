<a name="content">目录</a>

[树莓派GPIO入门](#title)
- [前言：GPIO引脚说明](#introduction-to-gpio)
- [01-使用GPIO接口控制发光二极管闪烁](#01-shrink-led)
- [02-GPIO控制LED亮度，制作呼吸灯效果](#02-control-led-brightness)
- [03-GPIO控制RGB彩色LED灯](#03-control-rgb-led)
- [04-使用按钮](#04-button)
- [05-驱动数码管显示数字](#05-digitron)
- [06-用超声波传感器测量距离](#06-measure-distance-by-ultrasonic-sensor)
- [07-红外感应模块 + 蜂鸣器实现简易报警](#07-alarm-consist-of-infrared-sensor-and-buzzer)


<h1 name="title">树莓派GPIO入门</h1>

<a name="introduction-to-gpio"><h2>前言：GPIO引脚说明 [<sup>目录</sup>](#content)</h2></a>

<p align="center"><img src=./picture/LearningGPIO-introduction-GPIO-1.jpg width=600 /></p>

可以在命令行执行`gpio readall`查看各个引脚的说明

<p align="center"><img src=./picture/LearningGPIO-introduction-GPIO-2.png width=600 /></p>

<a name="01-shrink-led"><h2>01-使用GPIO接口控制发光二极管闪烁 [<sup>目录</sup>](#content)</h2></a>

<table>
<tbody>
<tr>
	<td>最终效果</td>
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
	<td>最终效果</td>
	<td>所需材料</td>
</tr>
<tr>
	<td><p align="center"><img src=./picture/LearningGPIO-03-1.gif height=600 /></p></td>
	<td><p align="center"><img src=./picture/LearningGPIO-03-2.jpg height=600 /></p></td>
</tr>
</tbody>
</table>

**原理说明：**

这个RGB彩色LED里其实有3个灯，分别是红灯绿灯和蓝灯。控制这三个灯分别发出不同强度的光，混合起来就能发出各种颜色的光了。 LED灯上的4根引脚分别是VCC，R，G，B。 VCC需要接到电源正极。我们把它连到树莓派的5V引脚上。 R,G,B分别是红绿蓝灯的负极接口。我们把它们连接到树莓派的GPIO口上。 然后跟前一篇一样，使用PWM来控制3个小灯的明暗程度即可混合出各种不同颜色的光。

**硬件连接：**

<p align="center"><img src=./picture/LearningGPIO-03-3.png height=500 /></p>

<p align="center"><img src=./picture/LearningGPIO-03-4.png height=500 /></p>

**Python代码：**

```
#!/usr/bin/env python
# encoding: utf-8

import RPi.GPIO
import time

R,G,B=15,18,14

RPi.GPIO.setmode(RPi.GPIO.BCM)

RPi.GPIO.setup(R, RPi.GPIO.OUT)
RPi.GPIO.setup(G, RPi.GPIO.OUT)
RPi.GPIO.setup(B, RPi.GPIO.OUT)

pwmR = RPi.GPIO.PWM(R, 70)
pwmG = RPi.GPIO.PWM(G, 70)
pwmB = RPi.GPIO.PWM(B, 70)

pwmR.start(0)
pwmG.start(0)
pwmB.start(0)

try:

    t = 0.4
    while True:
        # 红色灯全亮，蓝灯，绿灯全暗（红色）
        pwmR.ChangeDutyCycle(0)
        pwmG.ChangeDutyCycle(100)
        pwmB.ChangeDutyCycle(100)
        time.sleep(t)
        
        # 绿色灯全亮，红灯，蓝灯全暗（绿色）
        pwmR.ChangeDutyCycle(100)
        pwmG.ChangeDutyCycle(0)
        pwmB.ChangeDutyCycle(100)
        time.sleep(t)
        
        # 蓝色灯全亮，红灯，绿灯全暗（蓝色）
        pwmR.ChangeDutyCycle(100)
        pwmG.ChangeDutyCycle(100)
        pwmB.ChangeDutyCycle(0)
        time.sleep(t)
        
        # 红灯，绿灯全亮，蓝灯全暗（黄色）
        pwmR.ChangeDutyCycle(0)
        pwmG.ChangeDutyCycle(0)
        pwmB.ChangeDutyCycle(100)
        time.sleep(t)
        
        # 红灯，蓝灯全亮，绿灯全暗（洋红色）
        pwmR.ChangeDutyCycle(0)
        pwmG.ChangeDutyCycle(100)
        pwmB.ChangeDutyCycle(0)
        time.sleep(t)
        
        # 绿灯，蓝灯全亮，红灯全暗（青色）
        pwmR.ChangeDutyCycle(100)
        pwmG.ChangeDutyCycle(0)
        pwmB.ChangeDutyCycle(0)
        time.sleep(t)
        
        # 红灯，绿灯，蓝灯全亮（白色）
        pwmR.ChangeDutyCycle(0)
        pwmG.ChangeDutyCycle(0)
        pwmB.ChangeDutyCycle(0)
        time.sleep(t)
        
        # 调整红绿蓝LED的各个颜色的亮度组合出各种颜色
        for r in xrange (0, 101, 20):
            pwmR.ChangeDutyCycle(r)
            for g in xrange (0, 101, 20):
                pwmG.ChangeDutyCycle(g)
                for b in xrange (0, 101, 20):
                    pwmB.ChangeDutyCycle(b)
                    time.sleep(0.01)

except KeyboardInterrupt:
    pass

pwmR.stop()
pwmG.stop()
pwmB.stop()

RPi.GPIO.cleanup()
```

<a name="04-button"><h2>04-使用按钮 [<sup>目录</sup>](#content)</h2></a>

用3个按钮来手动控制彩色LED灯分别发出红，绿，蓝光并可以同时按下不同按钮以显示混合颜色的光。

<p align="center"><img src=./picture/LearningGPIO-04-1.gif width=500 /></p>

<p align="center">效果图</p>

<p align="center"><img src=./picture/LearningGPIO-04-2.jpg width=500 /></p>

<p align="center">所需硬件</p>

<p align="center"><img src=./picture/LearningGPIO-04-3.png width=500 /></p>

<p align="center">硬件连接方式与原理</p>

**Python代码：**

```
#!/usr/bin/env python
# encoding: utf-8

import RPi.GPIO
import time

R,G,B=15,18,14

# 按钮输出针脚连接的GPIO口
btnR, btnG, btnB=21,20,16

RPi.GPIO.setmode(RPi.GPIO.BCM)

RPi.GPIO.setup(R, RPi.GPIO.OUT)
RPi.GPIO.setup(G, RPi.GPIO.OUT)
RPi.GPIO.setup(B, RPi.GPIO.OUT)

# 按钮连接的GPIO针脚的模式设置为信号输入模式，同时默认拉高GPIO口电平，
# 当GND没有被接通时，GPIO口处于高电平状态，取的的值为1
# 注意到这是一个可选项，如果不在程序里面设置，通常的做法是通过一个上拉电阻连接到VCC上使之默认保持高电平
RPi.GPIO.setup(btnR, RPi.GPIO.IN, pull_up_down=RPi.GPIO.PUD_UP)
RPi.GPIO.setup(btnG, RPi.GPIO.IN, pull_up_down=RPi.GPIO.PUD_UP)
RPi.GPIO.setup(btnB, RPi.GPIO.IN, pull_up_down=RPi.GPIO.PUD_UP)

try:

    RPi.GPIO.output(R, True)
    RPi.GPIO.output(G, True)
    RPi.GPIO.output(B, True)
    while True:
        time.sleep(0.01)
        
        # 检测按钮1是否被按下，如果被按下(低电平)，则亮红灯(输出低电平)，否则关红灯
        if (RPi.GPIO.input(btnR) == 0):
            RPi.GPIO.output(R, False)
        else:
            RPi.GPIO.output(R, True)
        
        # 检测按钮2是否被按下，如果被按下(低电平)，则亮绿灯(输出低电平)，否则关绿灯
        if (RPi.GPIO.input(btnG) == 0):
            RPi.GPIO.output(G, False)
        else:
            RPi.GPIO.output(G, True)
        
        # 检测按钮3是否被按下，如果被按下(低电平)，则亮蓝灯(输出低电平)，否则关蓝灯
        if (RPi.GPIO.input(btnB) == 0):
            RPi.GPIO.output(B, False)
        else:
            RPi.GPIO.output(B, True)

except KeyboardInterrupt:
    pass

RPi.GPIO.cleanup()
```

<a name="05-digitron"><h2>05-驱动数码管显示数字 [<sup>目录</sup>](#content)</h2></a>

用树莓派的GPIO口驱动数码管来显示数字，进而制作一个简单的电子钟，通过按钮来切换显示时间或日期

<p align="center"><img src=./picture/LearningGPIO-05-1.jpg width=500 /></p>

**原理说明**

标准的数码管从显示内容上分7段和8段数码管两种。8段比7段多一个右下角的小数点。还有一些其他特殊的如可以显示米字形的数码管不在本文讨论范围内，其实原理都是一样的。

8段数码管由8个发光二极管组成，其中7个用于组成数字，1个用于显示小数点。每一根的编号如下图的右上角所示(A-G,DP)。

<p align="center"><img src=./picture/LearningGPIO-05-2.jpg width=500 /></p>

数码管从电源极性上分**共阳**和**共阴**两种。

解释一下，如果数码管上每一个独立的发光二极管都单独引出两根引脚，一根接正极(阳)一根接负极(阴)，那么一个8段数码管就需要16根引脚来控制。但其实这8段数码管完全可以在内部共用一个阳级，只控制各段发光二极管的阴级联通即可，这就是共阳。反之亦然，叫共阴。共阳或共阴的每个8段数码管只需要引出9个引脚，1个阳(阴)级接到树莓派vcc(gnd)上，另外8个分别连到gpio口上，通过控制io口高低电平即可显示所需数字。比如一只共阳数码管想显示数字1，看LED编号图可知需要点亮b段和c段，其他全灭。那么连到共阳端引脚的io口输出高电平，连到引脚b、c的io口输出低电平，连到引脚a、d、e、f、g、dp的io口均输出高电平即可。写成代码就是：

```
# 定义各段发光二极管对应的GPIO口
LED_A = 26
LED_B = 19
LED_C = 13
LED_D = 6
LED_E = 5
LED_F = 11
LED_G = 9
LED_DP = 10

# 定义数码管共阳极对应的GPIO口
VCC = 12

# 避免闪烁，在输出数字字形信号前先拉低共阳端，关闭显示
RPi.GPIO.output(VCC, False)

# 输出数字1的字形信号
RPi.GPIO.output(LED_A, True)
RPi.GPIO.output(LED_B, False)
RPi.GPIO.output(LED_C, False)
RPi.GPIO.output(LED_D, True)
RPi.GPIO.output(LED_E, True)
RPi.GPIO.output(LED_F, True)
RPi.GPIO.output(LED_G, True)
RPi.GPIO.output(LED_DP, True)

# 最后拉高共阳段，显示数字
RPi.GPIO.output(VCC, True)
```

本文使用的数码管是8段共阳4位(4个数字)数码管，型号是F3461BH。上面说了共阳数码管每个数字需要9个引脚来控制，那么4个数字就需要36个引脚吗？显然不现实，树莓派的io口也完全不够用。这就引出另一个概念，**静态显示**和**动态扫描显示**。

**静态显示**：就是前面说的每一个数字需要占用8个io口，每多一个数字就需要额外的8个io口，如果数字位数不多，io口够用的话，这样做完全没问题。实际应用中往往需要显示多个数字，io口基本上是不够用的。这就需要动态扫描显示了。

> 数码管动态显示接口是单片机中应用最为广泛的一种显示方式之一，动态驱动是将所有数码管的8个显示笔划"a,b,c,d,e,f,g,dp"的同名端连在一起引出8个引脚，每个数字再单独引出共阳(阴)端，这样总引脚数就只要8 + 数字个数即可，本文使用的8段4位数码管正是引出了12个引脚。至于哪个引脚对应哪一段，哪几个引脚分别对应各数字的共阳(阴)端，就需要商家提供电路图了。当然也可以自己慢慢试，这不在本文讨论范围，大家可以自己摸索。
> 
> 当树莓派输出8个段信号时，所有数码管都会接收到相同的信号，但究竟是哪个数码管会显示出字形，取决于这个数码管对应的共阳(阴)极(后统称位选端)有无导通。所以我们只要将需要显示的数码管的位选端选通，该位就显示出字形，没有选通的数码管就不会亮。通过分时轮流控制各个数码管的的位选端，就使各个数码管轮流受控显示，这就是动态驱动。
> 
> 在轮流显示过程中，每位数码管的点亮时间为1～2ms，由于人的视觉暂留现象及发光二极管的余辉效应，尽管实际上各位数码管并非同时点亮，但只要扫描的速度足够快，给人的印象就是一组稳定的显示数据，不会有闪烁感，动态显示的效果和静态显示是一样的，能够节省大量的I/O端口，而且功耗更低。

<p align="center"><img src=./picture/LearningGPIO-05-3.png width=500 /></p>

**Python代码**

```
#!/usr/bin/env python
# encoding: utf-8

import RPi.GPIO
import time

# 定义单个数码管各段led对应的GPIO口
LED_A = 26
LED_B = 19
LED_C = 13
LED_D = 6
LED_E = 5
LED_F = 11
LED_G = 9
LED_DP = 10

# 定义1到4号数码管阳极对应的GPIO口
DIGIT1 = 12
DIGIT2 = 16
DIGIT3 = 20
DIGIT4 = 21

# 定义按钮输入的GPIO口
btn = 27

RPi.GPIO.setmode(RPi.GPIO.BCM)

RPi.GPIO.setup(LED_A, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_B, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_C, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_D, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_E, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_F, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_G, RPi.GPIO.OUT)
RPi.GPIO.setup(LED_DP, RPi.GPIO.OUT)
RPi.GPIO.setup(DIGIT1, RPi.GPIO.OUT)
RPi.GPIO.setup(DIGIT2, RPi.GPIO.OUT)
RPi.GPIO.setup(DIGIT3, RPi.GPIO.OUT)
RPi.GPIO.setup(DIGIT4, RPi.GPIO.OUT)

RPi.GPIO.output(DIGIT1, True)
RPi.GPIO.output(DIGIT2, True)
RPi.GPIO.output(DIGIT3, True)
RPi.GPIO.output(DIGIT4, True)

RPi.GPIO.setup(btn, RPi.GPIO.IN, pull_up_down=RPi.GPIO.PUD_UP)

# 指定no(1-4)号数码管显示数字num(0-9)，第三个参数是显示不显示小数点（true/false）
def showDigit(no, num, showDotPoint):
    # 先将正极拉低，关掉显示
    RPi.GPIO.output(DIGIT1, False)
    RPi.GPIO.output(DIGIT2, False)
    RPi.GPIO.output(DIGIT3, False)
    RPi.GPIO.output(DIGIT4, False)
    
    if (num == 0) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, False)
        RPi.GPIO.output(LED_F, False)
        RPi.GPIO.output(LED_G, True)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 1) :
        RPi.GPIO.output(LED_A, True)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, True)
        RPi.GPIO.output(LED_E, True)
        RPi.GPIO.output(LED_F, True)
        RPi.GPIO.output(LED_G, True)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 2) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, True)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, False)
        RPi.GPIO.output(LED_F, True)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 3) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, True)
        RPi.GPIO.output(LED_F, True)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 4) :
        RPi.GPIO.output(LED_A, True)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, True)
        RPi.GPIO.output(LED_E, True)
        RPi.GPIO.output(LED_F, False)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 5) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, True)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, True)
        RPi.GPIO.output(LED_F, False)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 6) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, True)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, False)
        RPi.GPIO.output(LED_F, False)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 7) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, True)
        RPi.GPIO.output(LED_E, True)
        RPi.GPIO.output(LED_F, True)
        RPi.GPIO.output(LED_G, True)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 8) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, False)
        RPi.GPIO.output(LED_F, False)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    elif (num == 9) :
        RPi.GPIO.output(LED_A, False)
        RPi.GPIO.output(LED_B, False)
        RPi.GPIO.output(LED_C, False)
        RPi.GPIO.output(LED_D, False)
        RPi.GPIO.output(LED_E, True)
        RPi.GPIO.output(LED_F, False)
        RPi.GPIO.output(LED_G, False)
        RPi.GPIO.output(LED_DP, not showDotPoint)
    
    if (no == 1) :
        RPi.GPIO.output(DIGIT1, True)
    elif (no == 2) :
        RPi.GPIO.output(DIGIT2, True)
    elif (no == 3) :
        RPi.GPIO.output(DIGIT3, True)
    elif (no == 4) :
        RPi.GPIO.output(DIGIT4, True)

try:
    t=0.005
    while True:
        # 按钮按下时显示日期，否则显示时间
        # 为了区别左右的数字，让第二个数码管的小数点显示出来
        #（本来应该是一个冒号，我们这个数码管没有，就用小数点代替了）
        if (RPi.GPIO.input(btn) == 1):
            time.sleep(t)
            showDigit(1, int(time.strftime("%H",time.localtime(time.time()))) / 10, False)
            time.sleep(t)
            showDigit(2, int(time.strftime("%H",time.localtime(time.time()))) % 10, True)
            time.sleep(t)
            showDigit(3, int(time.strftime("%M",time.localtime(time.time()))) / 10, False)
            time.sleep(t)
            showDigit(4, int(time.strftime("%M",time.localtime(time.time()))) % 10, False)
        else:
            time.sleep(t)
            showDigit(1, int(time.strftime("%m",time.localtime(time.time()))) / 10, False)
            time.sleep(t)
            showDigit(2, int(time.strftime("%m",time.localtime(time.time()))) % 10, True)
            time.sleep(t)
            showDigit(3, int(time.strftime("%d",time.localtime(time.time()))) / 10, False)
            time.sleep(t)
            showDigit(4, int(time.strftime("%d",time.localtime(time.time()))) % 10, False)
            
except KeyboardInterrupt:
    pass

# 最后清理GPIO口（不做也可以，建议每次程序结束时清理一下，好习惯）
RPi.GPIO.cleanup()
```

<a name="06-measure-distance-by-ultrasonic-sensor"><h2>06-用超声波传感器测量距离 [<sup>目录</sup>](#content)</h2></a>

<p align="center"><img src=./picture/LearningGPIO-06-1.jpg width=500 /></p>

上图所示的传感器有一个发射器和一个接收器，该传感器在400cm的范围之内的测距精度接近±3cm。

该传感器引出了4个引脚：接地引脚、回波引脚、触发引脚和供电引脚。接地引脚和5V供电引脚直接与树莓派相连。当我们从树莓派向传感器的触发引脚给出一个输入时，发射器就会发射声波脉冲，发出的声波脉冲被固体或物体表面发射回来后，我们就可以从回波引脚接受到回波脉冲。然后可以计算回波的往返时间，之后就可以算出距离了

超声波传感器需要在5V的电源下工作，树莓派上正好有5V的供电引脚。作为回应，超声波传感器会产生5V的回波信号给树莓派 —— 我们知道树莓派需要在GPIO上加上3.3V的电平才能安全工作，所以我们需要用到分亚器电路，将电压分为两部分：3.3V + 1.7V

<p align="center"><img src=./picture/LearningGPIO-06-2.jpg width=500 /></p>

**超声波传感器的工作原理**：从树莓派向传感器触发引脚发出一个10μs脉冲（通过将触发引脚设为持续时间为10μs的高电平来创建脉冲信号），一旦传感器接收到这个触发信号，它就会发出40kHz的超声波脉冲。在超声波脉冲发出后，我们应该等待回波信号（回波引脚在发出超声波脉冲之前保持低电平，当超声波脉冲发出之后变成高电平，并持续到收到回波信号为止），每个回波信号的长度为我们进行距离计算提供了计时。

**Python代码：**

```
#!/usr/bin/env python
# encoding: utf-8

import RPi.GPIO
import time

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)

Trig,Echo = 7,1

print("开始测距\n")
print("按Ctr+C退出\n")

GPIO.setup(Trig,GPIO.OUTPUT)
GPIO.setup(Echo,GPIO.INPUT)
time.sleep(0.02)

GPIO.output(Trig,False)
time.sleep(1)

try:
	while True:
		# 触发脉冲
		GPIO.output(Trig,True)
		time.sleep(0.00001)
		GPIO.output(Trig,False)
		# 等待回波脉冲
		while GPIO.input(Echo) == 0:
			Start_time = time.time()
		# 获取回波脉冲的持续时间
		while GPIO.input(Echo) ==1:
			End_time = time.time()
		# 计算距离
		Time = End_time - Start_time
		Distance = 17150 * Time
		print("当前距离为：%f cm",Distance)
except KeyboardInterrupt:
	pass

GPIO.cleanup()
```

<a name="07-alarm-consist-of-infrared-sensor-and-buzzer"><h2>07-红外感应模块 + 蜂鸣器实现简易报警 [<sup>目录</sup>](#content)</h2></a>

实现基本功能：运行脚本后，感应模块每隔一定时间检测，如有人靠近，则发出哔哔报警声，并在屏幕打印提示信息，人若离开，则停止鸣叫。

**原理：**

当有人靠近红外传感器时，传感器的OUT引脚就会输出高电平，否则保持低电平 —— 将与传感器的OUT引脚相连的树莓派引脚作为输入

将蜂鸣器的正极连接3.3V电源引脚，负极连接任意GPIO Pin引脚，该引脚作为输出，若该引脚为高电平，则蜂鸣器不响，调为低电平才响

<p align="center"><img src=./picture/LearningGPIO-07.jpg width=500 /></p>

**Python代码：**

```
import RPi.GPIO as GPIO
import time

# 初始化
def init(in,out):
	GPIO.setwarnings(False)
	GPIO.setmode(GPIO.BOARD)
	GPIO.setup(in,GPIO.IN)
	GPIO.setup(out,GPIO.OUT)
 
# 蜂鸣器鸣叫函数
def beep(in,out):
	while GPIO.input(in) == True:
		GPIO.output(out,False)
		time.sleep(0.5)
		GPIO.output(out,True)
		time.sleep(0.5)
# 感应器侦测函数
def detct(in,out):
	try:
		while True:
			# 如果感应器针脚输出为True，则打印信息并执行蜂鸣器函数
			if GPIO.input(in) == True:
				print("Someone isclosing!")
				beep(in,out)
			# 否则将蜂鸣器的针脚电平设置为HIGH
			else:
				GPIO.output(out,True)
				print("Nobody!")
	except KeyboardInterrupt:
		pass
 
if __name__ == "__main__":
	Infrared_in,Buzzer_out = 12,21
	print("开始红外检测\n")
	print("按Ctr+C退出\n")
	init(Infrared_in,Buzzer_out)
	detct(Infrared_in,Buzzer_out)
	# 脚本运行完毕执行清理工作
	GPIO.cleanup()
```

---

参考资料：

(1) [个人博客网站：芒果爱吃胡萝卜](http://blog.mangolovecarrot.net/)

(2) [CSDN：树莓派学习二（点亮LED灯）](https://blog.csdn.net/qq_38005186/article/details/73076067)

(3) [印度] Rushi Gajjar 《树莓派+传感器》，机械工业出版社

(4) [树莓派实验室：红外感应模块+蜂鸣器实现简易报警](http://shumeipai.nxez.com/2014/08/31/infrared-sensor-module-and-buzzer-alarm-achieve.html)

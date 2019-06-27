<a name="content">目录</a>

[计算机基础知识](#title)
- [1. 计算机发展历史](#history-of-computer)
- [2. 集成电路与数字电路](#integrated-circuit-and-digital-circuit)
- [3. 存储器](#storage-device)
    - [3.1. 机械硬盘](#hard-drive-disk)
    - [3.2. 固态硬盘](#solid-state-disk)





<h1 name="title">计算机基础知识</h1>

<a name="history-of-computer"><h2>1. 计算机发展历史 [<sup>目录</sup>](#content)</h2></a>

计算机（computer）俗称电脑，是现代一种用于高速计算的电子计算机器，可以进行**数值计算**，又可以进行**逻辑计算**，还具有**存储记忆**功能。是能够按照程序运行，自动、高速处理海量数据的现代化智能电子设备

人类历史上真正意义的第一台电子计算机于**1946年诞生**，如今其硬件技术已经发展到第四代

- **第1代：电子管数字机（1946—1958年）**

    逻辑元件采用的是**真空电子管**，主存储器采用汞延迟线、阴极射线示波管静电存储器、磁鼓、磁芯；外存储器采用的是磁带。软件方面采用的是机器语言、汇编语言。应用领域以军事和科学计算为主

    特点：体积大、功耗高、可靠性差。速度慢（一般为每秒数千次至数万次）、价格昂贵，但为以后的计算机发展奠定了基础

- **第2代：晶体管数字机（1958—1964年）**

    逻辑元件用**晶体管**代替了原先的真空电子管

    晶体管不仅能实现电子管的功能，又具有尺寸小、重量轻、寿命长、效率高、发热少、功耗低等优点

    使用晶体管后，电子线路的结构大大改观，制造高速电子计算机就更容易实现了

- **第3代：集成电路数字机（1964—1970年）**

    逻辑元件采用中、**小规模集成电路**（MSI、SSI），主存储器仍采用磁芯。软件方面出现了分时操作系统以及结构化、规模化程序设计方法

    特点：速度更快（一般为每秒数百万次至数千万次），而且可靠性有了显著提高，价格进一步下降，产品走向了通用化、系列化和标准化等。应用领域开始进入文字处理和图形图像处理领域

- **第4代：大规模集成电路机（1970年至今）**

    逻辑元件采用**大规模和超大规模集成电路**（LSI和VLSI）。软件方面出现了数据库管理系统、网络管理系统和面向对象语言等

    集成电路（integrated circuit）：

    > 一种微型电子器件或部件
    >
    > 采用一定的工艺，把一个电路中所需的晶体管、电阻、电容和电感等元件及布线互连一起，制作在一小块或几小块半导体晶片或介质基片上，然后封装在一个管壳内，成为具有所需电路功能的微型结构

<a name="integrated-circuit-and-digital-circuit"><h2>2. 集成电路与数字电路 [<sup>目录</sup>](#content)</h2></a>

集成电路，按其功能、结构的不同，可以分为**模拟集成电路**、**数字集成电路**和数/模混合集成电路三大类

模拟集成电路：

> 又称线性电路，用来产生、放大和处理各种模拟信号（指幅度随时间变化的信号。例如半导体收音机的音频信号、录放机的磁带信号等），其输入信号和输出信号成比例关系

数字集成电路：

> 用来产生、放大和处理各种数字信号（指在时间上和幅度上离散取值的信号。例如3G手机、数码相机、电脑CPU、数字电视的逻辑控制和重放的音频信号和视频信号）
>
> 用数字信号完成对数字量进行**算术运算**和**逻辑运算**
>
> 计算机是既可以进行数值计算，又可以进行逻辑计算的，这两种计算主要靠**CPU**来完成

<p align="center"><img src=./picture/Computer-Knowledge-01.png width=600 /></p>

CPU中重要的负责进行执行运算的部分叫做算术逻辑单元，它就是由数字电路的逻辑门构成的

逻辑门是数字逻辑电路的基本单元，通过控制高、低电平（分别代表逻辑上的“真”与“假”或二进制当中的“1”和“0”），从而实现逻辑运算。常见的逻辑门包括“与”门，“或”门，“非”门，“异或”等等。逻辑门可以组合使用实现更为复杂的逻辑运算

<p align="center"><img src=./picture/Computer-Knowledge-02.png width=600 /></p>

除了逻辑运算，还可以通过逻辑门的组合，进行简单的额数值运算，如使用逻辑门可以实现加法器：

>   输入为A和B，输出为S（和）和C（进位标志）
>
>   S=A⊕B，即输出和为输入的异或；
>
>   C=A+B，输入都为1时，结果有进位；
>
>   <p align="center"><img src=./picture/Computer-Knowledge-03.png width=600 /></p>
>
>   <p align="center"><img src=./picture/Computer-Knowledge-04.png width=300 /></p>

数字电路具有以下特点：

> **1、 同时具有算术运算和逻辑运算功能**
>
> 数字电路是以二进制逻辑代数为数学基础，使用二进制数字信号，既能进行算术运算又能方便地进行逻辑运算（与、或、非、判断、比较、处理等），因此极其适合于运算、比较、存储、传输、控制、决策等应用
>
> **2、 实现简单，系统可靠**
>
> 以二进制作为基础的数字逻辑电路，可靠性较强。电源电压大小的波动对其没有影响，温度和工艺偏差对其工作的可靠性影响也比模拟电路小得多
>
> **3、 集成度高，功能实现容易**
>
> 集成度高，体积小，功耗低是数字电路突出的优点之一。电路的设计、维修、维护灵活方便，随着集成电路技术的高速发展，数字逻辑电路的集成度越来越高

<a name="storage-device"><h2>3. 存储器 [<sup>目录</sup>](#content)</h2></a>

硬盘分为机械硬盘和固态硬盘

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-01.png width=600 /></p>

<a name="hard-drive-disk"><h3>3.1. 机械硬盘 [<sup>目录</sup>](#content)</h3></a>

对于机械硬盘，最重要的结构是这些两面涂有磁性材料的磁盘，在工作时会以每分钟 7200 转的速度旋转

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-02.png width=600 /></p>

写入数据时，距离盘面 3 纳米的磁头会利用电磁铁，改变磁盘上磁性材料的极性来记录数据，两种极性分别对应 0 或 1

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-03.gif width=600 /></p>

而读取数据时，旁边的读取器可以识别磁性材料的不同极性，再还原成 0 或 1 

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-04.gif width=600 /></p>

一片磁盘分为若干个磁道，每个磁道又分为各个扇区。扇区是磁盘存储的最小数据块，大小一般是 512 字节

因此，磁头要想读取某个文件，必须在电机驱动下，先找到对应的磁道，再等磁盘转到对应扇区才行

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-05.gif width=600 /></p>

一般会有十几毫秒的延迟，这就让机械硬盘在读取分散于磁盘各处的数据时，速度将大幅降低

<a name="solid-state-disk"><h3>3.2. 固态硬盘 [<sup>目录</sup>](#content)</h3></a>

前面提到机械硬盘存在的最大问题是读写的延迟问题

基于电路的固态硬盘则不用担心这种延迟，固态硬盘储存数据靠的是闪存。

在工作时，数据会通过接口进入主控制器，经处理后再分配到闪存中储存

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-06.gif width=600 /></p>

闪存的基本存储单元是浮栅晶体管，主要有这些结构

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-07.png width=600 /></p>

其中的浮栅被二氧化硅包裹，和上下绝缘

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-08.png width=600 /></p>

在断电时也能够保存电子，当电子数量高于一个中间值就表示 0 ，低于中间值就表示 1 

<p align="center"><img src=./picture/Computer-Knowledge-storage-device-09.png width=600 /></p>

如何写数据？

> 晶体管每次写入数据前都要先擦除，在 P 极上加一个电压，浮栅中原有的电子会因为量子隧穿效应通过绝缘层被吸出来，让浮栅中的电子数量低于中间值，还原成 1 
>
> <p align="center"><img src=./picture/Computer-Knowledge-storage-device-10.gif width=600 /></p>
>
> 如果要写入 0 ，就在控制极加一个电压，让电子穿过绝缘层再注回浮栅，使电子数量高于中间值，表示 0
>
> <p align="center"><img src=./picture/Computer-Knowledge-storage-device-11.gif width=600 /></p>

如何读数据？

> 在读取时，闪存无法直接得知浮栅中有多少电子，只能曲线救国
>
> 首先要知道，往控制极加一定大小的电压，会导通这两个 N 极。控制极上的电压越大，N 极间的电流也越大
>
> <p align="center"><img src=./picture/Computer-Knowledge-storage-device-12.gif width=600 /></p>
>
> 然而，存储 0 的浮栅，相比存储 1 的浮栅，有更多的电子，会抵消控制极上的电压，所以控制极需要更大的电压才能导通两个 N 极
>
> <p align="center"><img src=./picture/Computer-Knowledge-storage-device-13.gif width=600 /></p>
>
> 因此，当我们不知道浮栅中有多少电子时，就可以往控制极加一个中间值电压，如果两个 N 极导通，就能反推出浮栅中的电子较少，识别为 1；如果没有导通，就说明浮栅中的电子较多，识别为 0 
>
> <p align="center"><img src=./picture/Computer-Knowledge-storage-device-14.gif width=600 /></p>




---

参考资料：

(1) [漫话编程《漫话：如何给女朋友解释为什么计算机只认识0和1？》](https://mp.weixin.qq.com/s/9ePjft6QleS8pRz9AoXlmA)

(2) [booksyhay《第2.5章 使用门电路搭建加法器》](https://blog.csdn.net/booksyhay/article/details/80709192)

(3) [你的硬盘是如何储存数据的｜回形针](https://mp.weixin.qq.com/s/J3PgzRoHNSxzSqAnUKfRVg)

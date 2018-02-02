---
title: OmniVision Sensor 芯片
date: 2015-05-28 22:46:15
updated: 2015-05-28 22:46:15
comments: true
categories:
    - 数字图像
tags: [OmniVision, OV, sensor]
---

OmniVision
==============

OmniVision公司（简称OV），美商半导体公司，中文名豪威科技，成立于1995，专业开发高度集成 CMOS 影像技术。公司的创始人是经验丰富的工业专家，该公司主要创始人多半出身于摩托罗拉。2000前后，OmniVision的CMOS Sensor占据最大市场份额，然而时过境迁，现在是索尼的CMOS成为手机CMOS市场份额第一的厂商。如今的OV，难逃被收购的命运，目前已经被天朝财大气粗的公司收购了。

这段时间一直在研究OV9715 Sensor的配置。手头只有[DataSheet][DS]一份，而且是2009年！之后发现OV公司对技术的保密还是十分严谨，写的[DS][DS]也十分保守，N多register都“Reserved”却又是关键。如果你不是从正规渠道获取OV的产品，你是根本无法得到技术支持，网络上流出来的OV芯片配置，也都注释甚少。所以现在才明白，像这类技术公司，卖的不止芯片，还有支持。各种NDA，各种Reserved。

注；闭源的东西，如果你没钱，那东西就有许多坑。


OV9715 or OV9712
==============

Sensor架构
--------------
OV9715与OV9712是同一系列的芯片，架构、寄存器地址也都基本一致。要对芯片配置，还是有必要清楚其芯片的架构，知道reg作用于架构中哪一个部分，也就可以比较清晰地知道会reg会产生哪些作用。

![OV9715、OV9712 架构图](http://wx1.sinaimg.cn/large/00738gZoly1fnv6fbm1vsj30mq0eet9q.jpg)

架构主要被分为四大部分。每个模块各司其职

* image sensor core
* image sensor processor
* image output interface
* controller

----

### image sensor core
这是一个模数混合的部分。包括了Sensor、模拟gain、ADC、BLC、AEC、AGC、Colorbar、数字gain等等。多是挺多的，只是不是我研究方向。我目前只研究里面的分辨率、Colorbar。

![Sensor Array](http://wx2.sinaimg.cn/large/00738gZoly1fnwpoydg8yj30hc0ebabi.jpg)

#### Resolution
芯片第一个模块Sensor Array，[DS][DS]中reg说明如果有指明作用于Sensor，一般就都是指作用此模块。这个模块使用的是SYSCLK。最大可支持1280x800的分辨率，此模块的输出分辨率是可以进行修改的。

* 通过`reg 0x12`中的HT2、VT2，实现H V的降采样（DownSample）。当然是以一半来采样的啦\~ 也就是说：
    * 配置HT2，则经DSP后最终最大输出 640x800
    * 配置VT2，则经DSP后最终最大输出 1280x400
    * 配置HT2、VT2，则经DSP后最终最大输出 640x400
    * 不配置的话，就……  `:)`
* 通过`reg 0x17~0x1a 0x32 0x03`，可以配置Sensor Array的起点与输出pixel长度，这就实现了图像的第一次crop。

我的理解是，crop出来的图像分辨率，不可以超出DownSample之后分辨率。据流传出来的OV配置，配置Sensor输出给DSP的分辨率都要比最终输出略大一点。

#### Color Bar

* `reg 0x12`可以配置 color bar with pixel overlay.
* `reg 0x97`可以配置 color bar without pixel overlay. 配置了`reg 0x97`，那么`reg 0x12`就无效了。
* ColarBar其实是TestPattern其中一种，`reg 0xca`还可以对TestPattern配置。据我实际下板，配置TestPattern mode，出来的是很奇怪的细白竖条。选择“No test pattern”反而可以正常出ColorBar

#### 其他
研究了整个[DS][DS]的reg配置（许多Reserved不算），确认了OV9715只输出raw RGB。

----

### image sensor processor
这部分是不是可以简称为isp，与[DS][DS]中的ISP是否同一个意思，我暂时没有去考究。这部分也包含挺多模块，LENC、WBC、YAVG、sub-sample。这一堆也无暇折腾，我所要的，不过分辨率，PCLK，Frame Rate。这些坑，就让我一一道来吧。

* DSP Resolution。这是最终输出的分辨率，你要1280x800，就请准确配置`reg 0x57~0x59`
* PCLK。首先要知道**tp=1/SYSCLK**，然后[DS][DS]中又指出可配置为**PCLK=SYSCLK**，**PCLK=SYSCLK/2**。通过DSP的`reg 0x3b`DownSample就可以将PCLK配置为SYSCLK的一半。DownSample的作用就是只取一半的pixels呗。
    * 这里有个常见的640x400的常见配置，Sensor Horizontal size配置为1296，DSP使用DownSample并DSP Horizontal size就只配置640。好处就是获取Horizontal最大视野。
* fps。与诸多reg有关。就放置到DVP那边解释吧。

----

### image output interface
上图，不解释

![DVP Timing](http://wx3.sinaimg.cn/large/00738gZoly1fnwpoxh4k9j30ni09jjrw.jpg)

这部分与fps关系密切，[DS][DS]只是隐晦地说那些(1) (2) ...可以由reg进行配置，留下巨坑让你跳。其实我还没从这坑爬出来呢。

* (1)，也就是frame的时长。由(2) (3) (4) (5)共同组成。`reg 0x3d 0x3e`配置line / frame，即(1)中有多少个(4)。
* (2)，这个种类很多，要分情况配置。[DS][DS]的figure 6-2列出了3种
    * vsync_old （略）
    * vsync_new 可以由`reg 0xc8`配置
    * vsync3 可以由SOF，也可由vsync_widthx64。`reg 0xc9 0xca`可以配置vsync_width确定；SOF呢？由`reg 0x4b 0x4c`配置的吧，reg的单位与`reg 0x3d 0x3e`一样，line为单位
* (3)，好像不用配置
* (4)，`reg 0x2a 0x2b`配置，单位为 tp。这个是可以确定的
* (5)，`reg 0xcd~0xcf`，这个也是vsync3的24bit可编程延时。(5)也叫 eof2v_dly h2v_dly。
* (6)，这个是DSP Horizontal size。`reg 0x57 0x59`，单位为PCLK。
* (7)，=(4)-(6)。
* (8)、(9)这关键的两个东西，我目前还没搞清楚。在[DS][DS]中看到`reg 0x30 0x31`，但实际测试，修改这两个寄存器对(8) (9)不起作用。（坐等高人）

这些(1) (2)等要正确配置，否则就导致VSYNC信号一直高电平（或低电平）。我推算出来的fps计算式子：fps=sysclk/(`reg 0x3d 0x3e` * `reg 0x2a 0x2b`)。而且OV9712的最大输出是 1280x800@30fps（640x400@60fps），所以在配置fps要合理。

OV9712的HREF mode，即默认的mode，也是最常见的Sensor的输出时序。

OV9712的HSYNC mode，可以算是BT601建议的输出时序，在Vertical Blank中，也是有HSYNC信号输出。SOF之后第一个active line的延时（即(3)）是要知道嘀，以line为单位会比较符合BT601的标准，也更好控制些。(8)、(9)也是必须知道，方可正确取出active pixel。BT601建议的场信号，OV9712似乎并没有实现。

----

### controller
OV在IIC上折腾出SCCB总线，可2 wires，可3 wires。3 wires是为实现相同device ID，却能挂载多个Slave，或许还有其他目的，懒得知道\~你只要记住，SCCB这货是兼容IIC，足矣\~ 必须知道的：SCCB是reg addr与data都是8bit操作的。Device ID=0x60，OV9715与OV9712都使用相同的ID。

注：OV的power down模式，与MT9系统Sensor的Standby，控制方式刚好相反。OV的PWDN拉高即为power down；MT的Standby_n拉低即为power down。


与众不同
--------------

* vsync的极性，default是场消隐时为高电平；
* BT656并没有按照建议书传输yuv422，而是直接传输RawData。RawData值有可达到“0xFF”，“0x00”，这些与BT656中的视频计时基准码会冲突。当然，一般情况下还是可以正常工作的。


Reg配置参考
--------------
OV9715与OV9712的寄存器基本一致，网络上流传出来的芯片配置也主要是针对的OV9712，但对于OV9715还是有比较大的参考作用。

下面是我整合之后的reg配置

``` cpp
    // Core setting  不要问我，我什么都不知道
    iic_write_data(OV, 0x1e, 0x07);
    iic_write_data(OV, 0x5f, 0x18);
    iic_write_data(OV, 0x69, 0x04);
    iic_write_data(OV, 0x65, 0x2a);
    iic_write_data(OV, 0x68, 0x0a);
    iic_write_data(OV, 0x39, 0x28);
    iic_write_data(OV, 0x4d, 0x90);
    iic_write_data(OV, 0xc1, 0x80);
    // iic_write_data(OV, 0x0c, 0x30);
    // iic_write_data(OV, 0x6d, 0x02);
    // iic_write_data(OV, 0x4d, 0x90);
    // iic_write_data(OV, 0xc1, 0x80);

    //DSP
    iic_write_data(OV, 0x96, 0xf1); // AEC AWB ...
    // iic_write_data(OV, 0xbc, 0x68);

    /*  Resolution and Format  */
    iic_write_data(OV, 0x12, 0x00); // VT2[6] HT2[0] Sensor Down sample
    iic_write_data(OV, 0x3b, 0x01); // DSP Down sample，配置为1，使 PCLK=SYSCLK/2
    iic_write_data(OV, 0x97, 0x80); // 自己看DS
    iic_write_data(OV, 0x17, 0x25); // HStart MSB = 37*8+7  = 303，跳过黑边
    iic_write_data(OV, 0x18, 0xA2); // HSize MSB  = 0xA2*8+ 0 = 1296
    iic_write_data(OV, 0x32, 0x07); // HStart[2:0]LSB, HSize[5:3]LSB
    iic_write_data(OV, 0x19, 0x01); // VStart MSB = 1*4+ 2  = 6
    iic_write_data(OV, 0x1a, 0x78); // VSize MSB  = 120*4+2 = 482
    iic_write_data(OV, 0x03, 0x0a); // VStart[1:0]LSB, VSize[3:2]LSB
    iic_write_data(OV, 0x98, 0x00); // offset of pre_win out
    iic_write_data(OV, 0x99, 0x00);
    iic_write_data(OV, 0x9a, 0x00);
    iic_write_data(OV, 0x57, 0x00); /*DSP output HSize[4:2] VSize[1:0] LSB */
    iic_write_data(OV, 0x58, 0x78); /*DSP output VSize MSB = 120*4+0 = 480 */
    iic_write_data(OV, 0x59, 0x50); /*DSP output HSize MSB = 80*8+0 = 640 */
    iic_write_data(OV, 0x4c, 0x0b); // hi 640x400:0x99a     1280x800:0x1336
    iic_write_data(OV, 0x4b, 0x70); // low 经过示波器观察，是配置SOF、EOF
    iic_write_data(OV,0xbd, 0x50); // 0x50*8=640
    iic_write_data(OV,0xbe, 0x78); // 0x78*4=480
    // iic_write_data(OV,0x2c, 0x60); // I don't know.
    // iic_write_data(OV,0x23, 0x10);

    /*  Clock  */
    iic_write_data(OV,0x5c, 0x73);  // CLK1 = XCLK/PLL_pre_divider; PLL_pre_divider:REG5C[6:5]; CLK2 = CLK1*(32-REG5C[4:0])
    iic_write_data(OV,0x5d, 0x00);  // CLK3 = CLK2/(REG5D[3:2]+1)
    iic_write_data(OV,0x11, 0x00);  // SYSCLK = CLK3/((REG11[5:0]+1)*2)

    iic_write_data(OV,0x3e, 0x03);  // Line / Frame hi 此处是指有多少个(0x2a、0x2b)
    iic_write_data(OV,0x3d, 0x7f);  // Line / Frame low 是fps的一个配置量
    iic_write_data(OV,0x2b, 0x06);  // Tp of Hsync hi 1688
    iic_write_data(OV,0x2a, 0x98);  // Tp of Hsync low 我理解为包含行消隐的line长度
    // iic_write_data(OV,0xd0, 0x06);  // line length hi 疑点
    // iic_write_data(OV,0xd1, 0x98);  // Line length low

    iic_write_data(OV,0x2d, 0x00);  //Dummy line LSB DS中说是作用于Night Mode
    iic_write_data(OV,0x2e, 0x00);  //Dummy line MSB

    // DVP
    iic_write_data(OV,0xc2, 0x81);  // vsync3  vsync_width

    //General
    iic_write_data(OV,0x13, 0x85);
    iic_write_data(OV,0x14, 0x40); // analog gain
    iic_write_data(OV, 0x04, 0x48);// Vertical flip
```

疑点重重
--------------

1. HSYNC mode 中(8) (9)由哪个reg配置的？
1. `reg 0x30 0x31`与HSYNC mode有关系麽？与(8) (9)什么关系？
1. `reg 0xd0 0xd1`与`reg 0x2b 0x2a`之间是什么关系？
1. `reg 0xca`Test Pattern中的目的与作用是什么？

欢迎各位跟我讨论，更加欢迎帮我分析疑点。



[DS]: http://wenku.baidu.com/link?url=tKIHEE9K36GyX1_30pE7cLsVNOAbWWr3_jM8Z1bPZL2jEMdHn7d1xSPDdVjaF4tQAC3_tQquEMJ4eDLp4kvauLXKEC4Xymiz2i4e8T2ZDv7

IC 学习记录 / 2015年7月18日



设计流程
==============
1. 原理图或RTL设计
1. 功能仿真（行为级仿真）
1. 综合系统
1. 提取综合的单元特性，器件延时信息。进行带延时信息的仿真
1. Place & Route。在这个阶段，进一步提取含布线延时的时序信息
1. 可使用Synopsys Prime Time（PT）进行静态时序分析
1. 使用模拟（信号）仿真器进行细节仿真。生成GDSII文件（也叫stream文件）
1. 送至foundry制造商进行芯片制造。


Cadence EDA Tool
==============
Cadence公司设计平台DFII开发套件中，集成了众多EDA Tools（有些是被Cadence公司收购后集成到DFII平台中去的），这个DFII已经可以完成从原理图输入到芯片的整个开发过程。

Composer
--------------
全能的原理图输入工具

NC_Verilog
--------------
Verilog-XL是Cadence的一个解释仿真器。“解释”是指有一个运行的时间解释工具执行每一条Verilog指令并且与事件队列（Testbench）进行交流。

NC_Verilog是属于IUS的。NC_Verilog是Cadence的一个编译仿真器，它把Verilog代码编译成Verilog程序的定制仿真器。即它把Verilog代码转换成一个C程序，然后再把该C程序编译成仿真器。因此它启动稍微慢一些（需要转换与编译），但这样生成的编译仿真器运行要比Verilog-XL的解释仿真器快得多。所以被广泛使用。

SimVision
--------------
查看波形的软件

RTL Compiler
--------------
综合工具

Spectre
--------------
模拟仿真器。此工具也可以实现模拟/数字的混合仿真。同时还包含了功耗测量功能

SOC Encounter
--------------
布局布线工具。包含电源的布局、时钟树的综合等等东西

Virtuoso
--------------
版图设计工具


Synopsys EDA Tool
==============

VCS
--------------
VCS也是一个编译仿真器，功能强大，有众多选项配置

Design Compiler : DC
--------------
综合，要配置综合环境，配置Lib、配置约束。综合后可以查看面积、时序、功耗等。大多数的EDA Tool工具，都使用了Tcl脚本进行配置，包括DC。DC中还提供了许多IP（DesignWare）

Design Vision是一个DC的GUI软件工具。可以比较方便地查看关键路径等等。


PT
--------------


知识点
==============

1. 绝大多EDA Tool都运行之前都需要配置一定的软件环境（如：环境变量、Lib等等），Tool在使用之前，还需要配置Project（如：添加RTL文件、定义全局macro等等）。且不同的Tool可能还会有少许冲突，所以一般建议使用Script进行启动EDA Tools。
1. Latch与Flip-Flop的区别在于*寄存*时的触发方式。Latch是使用电平触发，FF是采用时钟触发；RTL开发时，要尽量避免使用Latch。
1. TestBench是指测试程序，属于软件；在集成电路的情况中，等测试芯片称为DUT（Device Under Test），原本是属于硬件的，但后来其术语含义也扩展到软件层面上。这样待测的RTL模块也可称为DUT。
1. Verilog仿真器一般是需要Verilog模型的？即使使用Schematic输入，也需要有Schematic对应的Verilog模型。
1. Schematic视图 与 Behavioral视图
1. VCS仿真，可以为代码设计断点，并可单步运行代码
1. 行为级仿真 与 开关级仿真 成对
1. 仿真时间选项，有具体延时（如：assign #2 a = b;）、零延时（唯一的延时是通过时钟控制寄存器的时钟级延时）、单位延时、SDF格式标注的延时（Standard Delay Format）。

Q & A
==============

1. 形式验证是什么？
1. DFT是如何插入的？是在RTL coding时就编写进去的吗？
1. CAD（computer aided design 计算机辅助设计）设计工具，与EDA（electronic design automation 电子设计自动化）工具，分别是指哪些？
1. UVM验证方法？
1. awk与sek是什么script？不是就两条command麽？
1. NC_Verilog 与 IUS 是关系是什么？ IUS与Cadence的开发平台DFII又有什么关系？
1. NC_Verilog仿真也是要生成仿真网表（netlist），这网表与综合的网表一不一样？
1. NC_Verilog即要编译Verilog、又要生成网表，是这样子的吗？
1. 仿真时有Behavioral视图、cmos_sch视图、functional视图。这些于我有什么区别？有什么作用？
1. DC的startup文件，包含了许多Library。什么target_library、synthetic_library、link_library、symbol_library。这些lib各自有什么作用？

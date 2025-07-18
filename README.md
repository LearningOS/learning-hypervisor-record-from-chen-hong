# learninghypervisor-record-from-chen-hong

## axvisor for LA 开发日志

*七月*

* [Day   1    (2025-07-10)](#0) 
* [Day   2    (2025-07-11)](#1) 
* [Day   3    (2025-07-12)](#2) 
* [Day   4    (2025-07-13)](#3)
* [Day   5    (2025-07-15)](#4)
* [Day   6    (2025-07-16)](#5)
* [Day   7    (2025-07-17)](#6)
* [Day   8    (2025-07-18)](#7)  

<span id="0"></span>

### Day 1

- 1.今天主要是阅读axvisor文档除设备相关部分，了解整体的架构。设备相关的内容后面涉及到了再回来补一补。文档里的图画的真好，对我梳理结构有很大帮助。对Axvisor API的后两种不是很理解，可能要具体的看一下\#[impl_interface]、\#[def_interface]等属性到底做了什么。

- 2.阅读axvisor的源代码，主要是根据main函数梳理一下虚拟机的工作流程，目前看了检测硬件支持和使能虚拟化两部分。使能虚拟化部分实现AxVCpuHal trait时使用了关联类型+完全限定的方式，从而实现上层调用不关心底层实现印象深刻。然后明天继续尝试阅读源代码完成对虚拟机工作流的梳理。

<span id="1"></span>

### Day 2

- 今天主要是完成了对axvisor工作流的梳理。整个梳理的过程中遇到比较大的问题是不清楚vmm具体在什么时刻开始执行起来的。这个地方卡了比较久，但是后来想起来hvisor中vcpu经过包装后可以被视作在cpu上运行的task。经此得到启发axvisor实际上是运行在底层arceos上的task,而vmm::init()为每个vmm的主vcpu创建对应的task（入口为vcpu_run）并将该task加入arceos的就绪队列。但是vcpu对应的task第一次运行时会因为对应的vmm未开启导致自身被挂起。而vmm::start()会修改vmm标志位启动vmm，同时将vmm的主vcpu唤醒（将主vcpu对应的task再次调度回arceos的就绪队列）。此时当vmm的vcpu对应的task再次获得时间片运行时，vmm就可以正常运转了。

预计明天从文件的角度整体阅读axvisor代码，然后应该还会开始一些新的学习。

![axvisor main函数执行流](photo_gallery/main().drawio.png)

<span id="2"></span>

### Day3

- 今天主要是完整的阅读了一下axvisor的代码总结了其中每文件的功能。然后补充了一些关于过程宏的知识。最后请教了苏助教crate发布到crate.io之前需要做哪些工作，苏助教讲解后为我推荐了repo和pr参考，以及可以尝试axvisor--crates中的axaddspace部分。预计明天会学习部分前几期训练营中的Hypervisor，然后阅读助教推荐的repo和pr。

![axvisor/src](photo_gallery/axvisor_src.drawio.png)

<span id="3"></span>

### Day4
今日进度：
- 1.完成了23年秋冬季训练营关于ARMv8的Hypervisor教学视频的学习，主要涉及armv8的寄存器介绍、二阶段地址翻译、及设备虚拟化中断虚拟化等。之后会学习该架构部分对应的实验文档以及完成里面的练习。
- 2.阅读了苏助教推荐axmm_crates源码，学习crate中example以及文档的编写，后续会继续分析该仓库提交的关键pr进一步学习如何完善crate，然后着手完善axaddrspace到可以发布carte.io。
另外明天暂停一天需要回学校答辩。

<span id="4"></span>

### Day5
今日进度：
- 1.阅读分析了axmm_crates仓库的关键pr，其中收获较大的是对MemoryAddr部分的代码重构部分。重构前会确切的定义地址空间结构体，并分别为不同的地址结构体实现大体相同的方法。重构后采用宏来声明定义地址数据结构，在trait中实现地址结构体的通用方法并为通过宏来定义的地质结构体实现，这样使得代码有更好的兼容性和可拓展性。另外axaddrspace中的protect函数好像没有实现明天尝试一下。
- 2.学习了部分AARCH64 Hypercraft教程，预计明天完成该教程学习。

<span id="5"></span>

### Day6

今日进度：
- 1.今天主要看了axaddrspace仓库代码，然后参考axmm中的protect实现补全了axaddrspace中的protect实现。

- 2.看完Hypercraft教程想跑一下对应的实验但是环境一直没搞定，明天求助一下助教。

<span id="6"></span>

### Day7

今天没有什么进展主要是解决了AARCH64 Hypercraft教程实验的环境配置问题把虚拟机跑起来了，然后在做ch2的实验的时候遇到问题，在完成虚拟机的vcpu初始化之前使用info输出信息会导致启动客户机时出现同步异常导致客户机启动失败，暂未确定具体原因是什么。

<span id="7"></span>

### Day8

今日进度：
- 1.初步编写了address_space/mod的测试模块仍需优化修改。

- 2.完成AARCH64 Hypercraft实验ch2和ch3部分。
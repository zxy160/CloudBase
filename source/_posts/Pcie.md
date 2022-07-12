---
title: Pcie学习
date: 2022-07-12 16:24:58
categories: Bios
---
[PCIe扫盲学习](http://blog.chinaaet.com/justlxy/p/5100053251)  
# 一、第一阶段
## 1.PCIE简介  
PCIe是继ISA和PCI总线之后的第三代I/O总线  。
第一代总线一般指ISA、EISA、VESA和Micro Platforms。
第二代总线一般指PCI、AGP和PCI-X。  
![](http://files.chinaaet.com/images/blog/2019/20180312/1000019445-6365644875352790836250675.png)   
从图上看感觉主要是速率上的提升。然后现在PCIe已经更新到第四代。
## 2.PCI总线基本概念  
PCI是的中文意思是外围器件互联，从结构上看，PCI是在CPU的供应商和原来系统总线之间插入的一级总线，具体由一个桥接电路实现对这一层的管理，并实现上下之间的接口以协调数据的传送。  
PCI总线是一种树形结构，并且独立于CPU总线，可以和CPU总线并行操作。PCI总线上可以挂接PCI设备和PCI桥，PCI总线上只允许一个PCI主设备（同一时刻），其他的均为PCI从设备，而且读写操作只能在主从设备之间进行，从设备之间的数据交换需要通过主设备中转。  
下图是X86架构  
![](http://files.chinaaet.com/images/blog/2019/20180313/1000019445-6365655222482740534418391.png)  
处理器连接在北桥上，其他就不用提示了。其中，通过仲裁器（Arbiter）来决定当前时刻的总线的控制权。
## 3.一个典型的PCI总线周期  
这一节的[网址](http://blog.chinaaet.com/justlxy/p/5100053078)  
感觉缺乏一些知识，不太能完全理解。
## 4.PCI总线中的Reflected-Wave Signaling  
PCI Spec规定了每个PCI总线上最多连接多达32个PCI设备，但是实际上一般只能连接10到12分负载。  
注释：插槽和PCI卡分别算作一个PCI设备。  
这都是因为PCI总线设计的时候，为了降低功耗，采用了一种叫作reflected‐wave signaling的技术。这个技术的原理：驱动信号电平达到实际需求的一半，然后依靠反射回来的信号叠加到原本的信号上。（不太理解，这样怎么会使得不能插太多pci卡的）  
那么如果要连接更多设备，可以使用PCI-to-PCI桥，每个桥内部都有隔离。一个PCI总线系统中，最多只能有256个子总线。
## 5.PCI总线中的三种传输模式  
Programmed I/O(PIO),Peer-to-Peer和DMA  
![](http://files.chinaaet.com/images/blog/2019/20180314/1000019445-6365661765962032514212992.png)  
如图中所示  
PIO  
某个PCI设备需要向内存（SDRAM）中写入一些数据，该PCI设备会想CPU请求一个中断，然后CPU首先通过PCi总线把该设备的数据读取到CPU内部的寄存器中，然后再把数据从内部寄存器写入到内存（SDRAM）中。PIO已经逐渐被DMA取代。  
DMA  
如图，以太网直接向内存（SDRAM）中写入数据。应用广泛。  
Peer-to-Peer  
这种方式的前提是，在PCI总线系统中至少有一个有能力成为主机设备。这种方式很少用，因为获得主机身份的PCI设备(Initiator)和另一个PCI设备（Target）通常采用不同的数据格式，除非他们是同一个厂家的设备。
## 6.PCI总线的中断和错误处理  
PCI中断信号属于边带信号，PCI设备不会去处理这些信号，需要发送给处理器，但是处理器不知道那些插槽上插着啥设备，这个问题需要bios来解决，通常用acpi表来记录。例如下面这样分类  
![](http://files.chinaaet.com/images/blog/2019/20180314/1000019445-6365661890883081567555898.png)
## 7.PCI总线的地址空间分配
PCI体系结构中，一共支持三种地址空间：Memory Address Space、I/O Address Space和Configuration Address Space。如下图  
![](http://files.chinaaet.com/images/blog/2019/20180328/1000019445-6365786219354249748277571.png)  
图中访问Configuration Address Space需要通过索引IO寄存器来完成。  
注：在PCIe中，则引入了一种新的Configuration Address Space访问方式：将其直接映射到了Memory Address Space当中。  
对于PCI的配置周期来说，包含了两个步骤：  
Step1：CPU先对IO Address中的0xCF8~0xCFB写入要操作的配置寄存器的地址。如下图所示，其中包括了总线号（Bus Number）、设备号（Device Number）、功能号（Function Number）和寄存器指针。  
Step2：CPU向IO Address中的0xCFC~0xCFF中写入读或者写的数据。  
![](http://files.chinaaet.com/images/blog/2019/20180328/1000019445-6365786223226438629578477.png)  
## 8.PCI总线配置周期产生和配置寄存器  
每个PCI功能都包含256个字节的配置空间(Configuration Space)，其中前64个字节被称为Header，剩余的192个字节用于一些可选功能。PCI Spec规定了两种类型的Header：Type1和Type0。其中Type1是桥，Type0是PCI设备功能。如下图  
![](http://files.chinaaet.com/images/blog/2019/20180328/1000019445-6365786224956266384094413.png)  
![](http://files.chinaaet.com/images/blog/2019/20180328/1000019445-6365786224968767356822892.png)
## 9.66MHz的PCI总线与其技术瓶颈  
因为PCI总线采用了Reflected-Wave Signaling技术，因此总线频率的提高，必然导致总线负载能力降低（不太懂），结果就是66MHz 64-bit的PCI总线，只能支持一个PCI插卡设备。  
因为有PCI总线采用的是non-registered输入，输入信号建立需要3ns，而66MHz的时钟周期为15ns，剩余12ns基本都被Reflected-Wave Signaling给消耗了，因此，66MHz基本上算是PCI总线的频率上限了。  
PCI-X总线，为了解决上面的问题，所有的输入信号都先用寄存器打一拍，所以PCI-X总线可以运行在更高的频率上（100MHz，甚至133MHz）
## 10.PCI-X总线基本概念  

## 11.PCIe总线基本概念
## 12.PCIe总线怎样做到在软件上兼容PCI总线
## 13.PCIe总线结构体系入门
## 14.PCIe总线事物层入门（一）
## 15.PCIe总线事物层入门（二）
## 16.PCIe总线事务层入门（三）
## 17.PCIe总线数据链路层入门
## 18.PCIe总线物理层入门
## 19.一个Memory Read操作的例子
# DFP_Mode
- 下面展示的样例来自于Keil的官方文档


  
## DFP 的创建

### 1） .PDSC 文件
<details>
  
#### 1.0）介绍

- CMSIS Pack 描述文件（Pack Description File）是 CMSIS-Pack 系统中的核心文件，用于描述和定义一个设备家族包（DFP, Device Family Pack）或其他软件包的内容和结构。
- 它通过 XML 格式来表达，包含关于设备、组件、库、驱动程序、示例代码等的信息，并为开发工具（如 Keil MDK、IAR 等）提供用于集成和管理这些资源的元数据。

#### 1.1）参考文档

- [CMSIS-Pack Components Documentation](https://www.keil.com/pack/doc/CMSIS_Dev/Pack/html/cp_SWComponents.html)
- [CMSIS-Pack DFP Creation Guide](https://www.keil.com/pack/doc/CMSIS_Dev/Pack/html/createPack_DFP.html#:~:text=A%20Software%20Pack%20that%20contains%20a%20%3Cdevices%3E%20element,device%20or%20a%20device%20family%20in%20more%20detail.)

#### 1.2）命名格式

- `Vendor.Name.pdsc`
- `Vendor` 为供应商名，在 `PACK.xsd` 中有登记，开发过程中使用“Generic:5”代替。
- `Name` 为家族名，如芯片 MVCM3 包含 MVCM3100 和 MVCM3200，则 `Name` 为 MVCM3。

#### 1.3）文件格式

##### 1.3.1）结构示例
```xml
<?xml version="1.0" encoding="UTF-8"?> <!--表示该文件使用 XML 1.0 标准并采用 UTF-8 编码。-->

<package schemaVersion="1.4.0" xmlns:xs="http://www.w3.org/2001/XMLSchema-instance" xs:noNamespaceSchemaLocation="PACK.xsd">
<!--定义整个软件包；指定该 .pdsc 文件所遵循的 CMSIS Pack 描述文件的架构版本；根据URL和PACK.xsd验证XML文档结构是否符合规范-->

    <vendor></vendor>                   <!--供应商-->
    <name></name>                       <!--家族名-->

    <url></url>                         <!--定义包的下载路径（若为空则Pack Installer显示Offline）-->
    <description></description>         <!--描述-->
    <supportContact></supportContact>   <!--联系方式联系方式-->
    <license>Docs/license.txt</license> <!--许可文件-->
    <releases></releases>               <!--发行的版本日期等信息-->
    <keywords></keywords>               <!--搜索用关键词-->

    <devices></devices>                 <!--见1.32)-->
    <conditions></conditions>           <!--见1.32)-->
    <components></components>           <!--见1.32)-->

</package>
```
##### 1.3.2）关键部分示例：
<details>
  
- 在下面的部分中，将为来自设备供应商MyVendor的虚拟设备族MVCM3创建DFP。
- 设备族由四个成员组成，分为两个子族。
- mvcm3系列的规格如下:  
![loading](asset/MVCM3.png "官方示例")

- 设备信息（devices）
```xml
<devices>
    <family Dfamily="MVCM3 Series" Dvendor="Generic:5">
    <processor Dcore="Cortex-M3" DcoreVersion="r2p1" Dfpu="0" Dmpu="0" Dendian="Little-endian"/>
    <description>
        The MVCM3 device family contains an ARM Cortex-M3 processor, running up to 100 MHz with a versatile set of on-chip peripherals.
    </description>
    <!-- ************************  Sub-family 'MVCM3100'  **************************** -->
    <subFamily DsubFamily="MVCM3100">
        <processor  Dclock="50000000"/>
        <!-- *************************  Device 'MVCM3110'  ***************************** -->
        <device Dname="MVCM3110">
        <memory     name="IROM1"  access="rx"       start="0x00000000"  size="0x4000"     startup="1"   default="1"/>
        <memory     name="IRAM1"  access="rw"       start="0x20000000"  size="0x0800"     init   ="0"   default="1"/>
        </device>
        <!-- *************************  Device 'MVCM3120'  ***************************** -->
        <device Dname="MVCM3120">
        <memory     name="IROM1"  access="rx"        start="0x00000000"  size="0x8000"     startup="1"   default="1"/>
        <memory     name="IRAM1"  access="rx"        start="0x20000000"  size="0x1000"     init   ="0"   default="1"/>
        </device>
    </subFamily>
    <!-- ************************  Sub Family 'MVCM3200'  **************************** -->
    <subFamily DsubFamily="MVCM3200">
        <processor  Dclock="100000000"/>
        <!-- *************************  Device 'MVCM3250'  ***************************** -->
        <device Dname="MVCM3250">
        <memory     name="IROM1"  access="rx"         start="0x00000000"  size="0x4000"     startup="1"   default="1"/>
        <memory     name="IRAM1"  access="rw"         start="0x20000000"  size="0x0800"     init   ="0"   default="1"/>
        </device>
        <!-- *************************  Device 'MVCM3260'  ***************************** -->
        <device Dname="MVCM3260">
        <memory     name="IROM1"  access="rx"         start="0x00000000"  size="0x8000"     startup="1"   default="1"/>
        <memory     name="IRAM1"  access="rw"         start="0x20000000"  size="0x1000"     init   ="0"   default="1"/>
        </device>
    </subFamily>
    </family>
</devices>
```
- 条件（conditions）
```xml
<conditions>
    <condition id="MVCM3 CMSIS-Core">
    <!-- conditions selecting Devices -->
    <description>MyVendor MVCM3 Series devices and CMSIS-Core (Cortex-M)</description>
    <require Cclass="CMSIS" Cgroup="Core"/>
    <require Dvendor="Generic:5" Dname="MVCM3*"/>
    </condition>
    
    <condition id="Startup ARM">
    <description>Startup assembler file for ARMCC</description>
    <require Tcompiler="ARMCC"/>
    </condition>

    <condition id="Startup GCC">
    <description>Startup assembler file for GCC</description>
    <require Tcompiler="GCC"/>
    </condition>

    <condition id="Startup IAR">
    <description>Startup assembler file for IAR</description>
    <require Tcompiler="IAR"/>
    </condition>
</conditions>
```                
- 组件（components）
```xml
<components>
    <component Cclass="Device" Cgroup="Startup" Cversion="0.0.1" condition="MVCM3 CMSIS-Core">
    <description>System Startup for MyVendor MVCM3 Series</description>
    <files>
        <!--  include folder -->
        <file category="include" name="Device/Include/"/>
        <file category="source"  name="Device/Source/ARM/startup_MVCM3.s" attr="config" condition="Startup ARM" version="1.0.0"/>
        <file category="source"  name="Device/Source/GCC/startup_MVCM3.s" attr="config" condition="Startup GCC" version="1.0.0"/>
        <file category="source"  name="Device/Source/IAR/startup_MVCM3.s" attr="config" condition="Startup IAR" version="1.0.0"/>
        <file category="source"  name="Device/Source/system_MVCM3.c"  attr="config" version="1.0.0"/>
    </files>
    </component>
</components>
```
</details>
</details>

### 2） DFP文件结构  
<details>  

#### 2.1）示例：
  
![loading](asset/struct.png "示例结构")

#### 2.2）Device:

- 介绍：放置启动文件（startup_device.s）、系统文件（system_device.c system_device.h）、寄存器文件（device.h）以及库函数等文件。
- 示例：
  
  ![loading](asset/device.png "device")
  ![loading](asset/source.png "source")
  ![loading](asset/include.png "include")
  ![loading](asset/startup.png "startup")

##### 2.2.1）startup_device.s：

- 设备上电或复位后执行的第一段代码。它负责在硬件复位后初始化堆栈、设置中断向量表、初始化全局和静态变量，并最终跳转到主程序的 main() 函数。

##### 2.2.2）system_device.c：

- 设备初始化的核心文件，负责对系统时钟、外设时钟、内存布局等进行配置。包含了 SystemInit() 函数，该函数在设备启动时被 startup_device.s 调用，进行硬件初始化。

##### 2.2.3）system_device.h：

- 为 system_device.c 中的系统初始化函数提供声明，同时包含一些与系统初始化相关的常量、宏和外部变量声明（如 SystemCoreClock）。

##### 2.2.4）device.h：

- 定义设备的外设寄存器映射、位域、内存地址等内容，使开发者可以通过结构体和宏访问设备外设。
- 可以通过CMSIS提供的SVDConv工具依据你的SVD文件直接生成

#### 2.3）Flash:
- 参考文档：
  - [Pack with Device Support](https://www.keil.com/pack/doc/CMSIS_Dev/Pack/html/createPack_DFP.html#:~:text=A%20Software%20Pack%20that%20contains%20a%20%3Cdevices%3E%20element,device%20or%20a%20device%20family%20in%20more%20detail.)
  - [Flash Programming](https://www.keil.com/pack/doc/CMSIS_Dev/Pack/html/flashAlgorithm.html)
  - [利用MDK的FLM文件制作通用flash驱动](https://cloud.tencent.com/developer/article/2230443)
  - [从零编写STM32H7的MDK SPI FLASH下载算法](https://mp.weixin.qq.com/s/IpEG3jN-Nx4Dm1ETxNBkgg)
- 示例：
![loading](asset/flash.png "flash")

##### 2.3.1）操作流程：  

- 1）将以下文件复制到你的 Vendor.Device.Version/Flash下
  - 一般位于Keil_v5\ARM\Flash\_Template路径下（v540版本）
  - 其中的FlashOS.h位于上级目录（v540版本）
![loading](asset/flash0.png "flash0") 
- 2）修改其中的三个工程文件，将“NewDevice”替换为你的设备名
- 3）修改其中的FlashDev.c和FlashPrg.c文件
- 4）通过Keil生成FLM文件

##### 2.3.2）修改FlashDev.c：  
- 1）介绍：
  - 定义一个FlashDevice结构体用于描述 Flash 设备的详细特性描述，包括设备的大小、扇区布局、页大小等信息。
  - 为工具提供必要的元数据，以便在进行 Flash 操作（如擦除、编程、读取）时，能够正确处理设备的存储布局。
- 2）示例：
  ```c
    struct FlashDevice const FlashDevice  =  {
       FLASH_DRV_VERS,             // 驱动版本
       "Example Flash",            // 设备名称
       ONCHIP,                     // 设备类型（片上 Flash）
       0x08000000,                 // 起始地址
       0x00100000,                 // 总大小 (1MB)
       256,                        // 页大小
       0,                          // 保留
       0xFF,                       // 擦除后的默认值
       5000,                       // 编程超时（毫秒）
       10000,                      // 擦除超时（毫秒）
       
       // 扇区布局
       0x08000000, 0x1000,    // 每个扇区 4KB
       0x08010000, 0x20000,   // 每个扇区 128KB
       SECTOR_END 
    };
  ```

##### 2.3.3）修改FlashPrg.c：  
- 1）介绍：
  - Keil 环境下的 Flash 编程算法的实现文件，它通过与 Flash 控制器的直接交互来完成擦除、写入、校验等操作。
- 2）示例：
  ```c
  int Init (unsigned long adr, unsigned long clk, unsigned long fnc) {
      // 初始化 Flash 编程设置，如地址和时钟频率
      // 配置寄存器，准备擦除或编程操作
      return 0;
  }
  
  int UnInit (unsigned long fnc) {
      // 反初始化，释放 Flash 资源
      return 0;
  }

  int EraseSector (unsigned long adr) {
      // 擦除指定地址的扇区
      return 0;
  }
  
  int EraseChip (void) {
      //擦除整个 Flash 存储器
      return 0;
  }
  
  int ProgramPage (unsigned long adr, unsigned long sz, unsigned char *buf) {
      // 将数据写入指定的 Flash 页面
      return 0;
  }
  ```
##### 2.3.4）生成FLM：  
- 1）打开Flash文件夹的工程
  ![loading](asset/flash1.png "flash1")
- 2）将输出文件命名为设备名
  ![loading](asset/flash2.png "flash2")
- 3）编译
- 4）在PDSC文件中添加FLM文件的路径,如下：
```xml
<device Dname="MVCM3110">
  <algorithm name="Flash/MVCM3.FLM" start="0x00000000" size="0x4000" default="1"/>
  <memory     name="IROM1"  access="rx"       start="0x00000000"  size="0x4000"     startup="1"   default="1"/>
  <memory     name="IRAM1"  access="rw"       start="0x20000000"  size="0x0800"     init   ="0"   default="1"/>
</device>
``` 
- 5）重新生成pack并导入，在对应的工程中可以看到
  ![loading](asset/flash3.png "flash3")
#### 2.4）SVD:

![loading](asset/svd.png "svd")
##### 2.4.1）介绍:
- 定义：SVD（System View Description） 文件是 CMSIS（Common Microcontroller Software Interface Standard） 中的定义的一个 XML 格式的纯文本文件，其包含了芯片内核、芯片所具有的外设以及内核和外设的寄存器的完整描述
- 作用：
  - 生成头文件（device.h）：使用相关工具命令（SVDConv.exe device.svd -o Output -b Output/device.log --generate=header）就可以根据编写的 SVD 文件直接生成芯片的顶级头文件（device.svd --> device.h）
  - 调试 ：SVD 文件最主要的目的是调试时直接显示寄存器的每个比特位的状态。
- 参考文档：
  - [TIH64 之一 编写 TIH64Vx690 的 SVD 文件及使用 SVD 辅助调试_svd 和 sfr文件的区别](https://blog.csdn.net/zcshoucsdn/article/details/127144450)
  - [System View Description](https://www.keil.com/pack/doc/CMSIS_Dev/SVD/html/index.html)

##### 2.4.2）文件格式:
- 参考文档：
  - [SVD Description (*.svd) Format](https://www.keil.com/pack/doc/CMSIS_Dev/SVD/html/svd_Format_pg.html)
- 示例：
  <details>
```xml
    <?xml version="1.0" encoding="utf-8"?>

<!-- File naming: <part/series name>.svd -->

<!--
  Copyright (C) 2012-2014 ARM Limited. All rights reserved.

  Purpose: System Viewer Description (SVD) Example (Schema Version 1.1)
           This is a description of a none-existent and incomplete device
		   for demonstration purposes only.
		   
  Redistribution and use in source and binary forms, with or without
  modification, are permitted provided that the following conditions are met:
   - Redistributions of source code must retain the above copyright
     notice, this list of conditions and the following disclaimer.
   - Redistributions in binary form must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.
   - Neither the name of ARM nor the names of its contributors may be used 
     to endorse or promote products derived from this software without 
     specific prior written permission.

  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" 
  AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE 
  IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
  ARE DISCLAIMED. IN NO EVENT SHALL COPYRIGHT HOLDERS AND CONTRIBUTORS BE
  LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
  CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF 
  SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS 
  INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN 
  CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) 
  ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
  POSSIBILITY OF SUCH DAMAGE.
 -->
 
<device schemaVersion="1.1" xmlns:xs="http://www.w3.org/2001/XMLSchema-instance" xs:noNamespaceSchemaLocation="CMSIS-SVD.xsd" >
  <vendor>ARM Ltd.</vendor>                                       <!-- device vendor name -->
  <vendorID>ARM</vendorID>                                        <!-- device vendor short name -->
  <name>ARM_Example</name>                                        <!-- name of part-->
  <series>ARMCM3</series>                                         <!-- device series the device belongs to -->
  <version>1.2</version>                                          <!-- version of this description, adding CMSIS-SVD 1.1 tags -->
  <description>ARM 32-bit Cortex-M3 Microcontroller based device, CPU clock up to 80MHz, etc. </description>
  <licenseText>                                                   <!-- this license text will appear in header file. \n force line breaks -->
    ARM Limited (ARM) is supplying this software for use with Cortex-M\n
    processor based microcontroller, but can be equally used for other\n
    suitable  processor architectures. This file can be freely distributed.\n
    Modifications to this file shall be clearly marked.\n
    \n
    THIS SOFTWARE IS PROVIDED "AS IS".  NO WARRANTIES, WHETHER EXPRESS, IMPLIED\n
    OR STATUTORY, INCLUDING, BUT NOT LIMITED TO, IMPLIED WARRANTIES OF\n
    MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE APPLY TO THIS SOFTWARE.\n
    ARM SHALL NOT, IN ANY CIRCUMSTANCES, BE LIABLE FOR SPECIAL, INCIDENTAL, OR\n
    CONSEQUENTIAL DAMAGES, FOR ANY REASON WHATSOEVER.
  </licenseText>
  <cpu>                                                           <!-- details about the cpu embedded in the device -->
    <name>CM3</name>
    <revision>r1p0</revision>
    <endian>little</endian>
    <mpuPresent>true</mpuPresent>
    <fpuPresent>false</fpuPresent>
    <nvicPrioBits>3</nvicPrioBits>
    <vendorSystickConfig>false</vendorSystickConfig>
  </cpu>
  <addressUnitBits>8</addressUnitBits>                            <!-- byte addressable memory -->
  <width>32</width>                                               <!-- bus width is 32 bits -->
  <!-- default settings implicitly inherited by subsequent sections -->
  <size>32</size>                                                 <!-- this is the default size (number of bits) of all peripherals
                                                                       and register that do not define "size" themselves -->
  <access>read-write</access>                                     <!-- default access permission for all subsequent registers -->
  <resetValue>0x00000000</resetValue>                             <!-- by default all bits of the registers are initialized to 0 on reset -->
  <resetMask>0xFFFFFFFF</resetMask>                               <!-- by default all 32Bits of the registers are used -->

  <peripherals>
    <!-- Timer 0 -->
    <peripheral>
      <name>TIMER0</name>
      <version>1.0</version>
      <description>32 Timer / Counter, counting up or down from different sources</description>
      <groupName>TIMER</groupName>
      <baseAddress>0x40010000</baseAddress>
      <size>32</size>
      <access>read-write</access>

      <addressBlock>
        <offset>0</offset>
        <size>0x100</size>
        <usage>registers</usage>
      </addressBlock>

      <interrupt>
        <name>TIMER0</name>
        <description>Timer 0 interrupt</description>
        <value>0</value>
      </interrupt>

      <registers>
      <!-- CR: Control Register -->
        <register>
          <name>CR</name>
          <description>Control Register</description>
          <addressOffset>0x00</addressOffset>
          <size>32</size>
          <access>read-write</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0x1337F7F</resetMask>

          <fields>
            <!-- EN: Enable -->
            <field>
              <name>EN</name>
              <description>Enable</description>
              <bitRange>[0:0]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Disable</name>
                  <description>Timer is disabled and does not operate</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Enable</name>
                  <description>Timer is enabled and can operate</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- RST: Reset -->
            <field>
              <name>RST</name>
              <description>Reset Timer</description>
              <bitRange>[1:1]</bitRange>
              <access>write-only</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>No_Action</name>
                  <description>Write as ZERO if necessary</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Reset_Timer</name>
                  <description>Reset the Timer</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- CNT: Counting Direction -->
            <field>
              <name>CNT</name>
              <description>Counting direction</description>
              <bitRange>[3:2]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Count_UP</name>
                  <description>Timer Counts UO and wraps, if no STOP condition is set</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Count_DOWN</name>
                  <description>Timer Counts DOWN and wraps, if no STOP condition is set</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Toggle</name>
                  <description>Timer Counts up to MAX, then DOWN to ZERO, if no STOP condition is set</description>
                  <value>2</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- MODE: Operation Mode -->
            <field>
              <name>MODE</name>
              <description>Operation Mode</description>
              <bitRange>[6:4]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Continous</name>
                  <description>Timer runs continously</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Single_ZERO_MAX</name>
                  <description>Timer counts to 0x00 or 0xFFFFFFFF (depending on CNT) and stops</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Single_MATCH</name>
                  <description>Timer counts to the Value of MATCH Register and stops</description>
                  <value>2</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Reload_ZERO_MAX</name>
                  <description>Timer counts to 0x00 or 0xFFFFFFFF (depending on CNT), loads the RELOAD Value and continues</description>
                  <value>3</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Reload_MATCH</name>
                  <description>Timer counts to the Value of MATCH Register, loads the RELOAD Value and continues</description>
                  <value>4</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- PSC: Use Prescaler -->
            <field>
              <name>PSC</name>
              <description>Use Prescaler</description>
              <bitRange>[7:7]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Disabled</name>
                  <description>Prescaler is not used</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Enabled</name>
                  <description>Prescaler is used as divider</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- CNTSRC: Timer / Counter Soruce Divider -->
            <field>
              <name>CNTSRC</name>
              <description>Timer / Counter Source Divider</description>
              <bitRange>[11:8]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>CAP_SRC</name>
                  <description>Capture Source is used directly</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div2</name>
                  <description>Capture Source is divided by 2</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div4</name>
                  <description>Capture Source is divided by 4</description>
                  <value>2</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div8</name>
                  <description>Capture Source is divided by 8</description>
                  <value>3</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div16</name>
                  <description>Capture Source is divided by 16</description>
                  <value>4</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div32</name>
                  <description>Capture Source is divided by 32</description>
                  <value>5</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div64</name>
                  <description>Capture Source is divided by 64</description>
                  <value>6</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div128</name>
                  <description>Capture Source is divided by 128</description>
                  <value>7</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>CAP_SRC_div256</name>
                  <description>Capture Source is divided by 256</description>
                  <value>8</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- CAPSRC: Timer / COunter Capture Source -->
            <field>
              <name>CAPSRC</name>
              <description>Timer / Counter Capture Source</description>
              <bitRange>[15:12]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>CClk</name>
                  <description>Core Clock</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_0</name>
                  <description>GPIO A, PIN 0</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_1</name>
                  <description>GPIO A, PIN 1</description>
                  <value>2</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_2</name>
                  <description>GPIO A, PIN 2</description>
                  <value>3</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_3</name>
                  <description>GPIO A, PIN 3</description>
                  <value>4</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_4</name>
                  <description>GPIO A, PIN 4</description>
                  <value>5</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_5</name>
                  <description>GPIO A, PIN 5</description>
                  <value>6</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_6</name>
                  <description>GPIO A, PIN 6</description>
                  <value>7</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOA_7</name>
                  <description>GPIO A, PIN 7</description>
                  <value>8</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOB_0</name>
                  <description>GPIO B, PIN 0</description>
                  <value>9</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOB_1</name>
                  <description>GPIO B, PIN 1</description>
                  <value>10</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOB_2</name>
                  <description>GPIO B, PIN 2</description>
                  <value>11</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOB_3</name>
                  <description>GPIO B, PIN 3</description>
                  <value>12</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOC_0</name>
                  <description>GPIO C, PIN 0</description>
                  <value>13</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOC_5</name>
                  <description>GPIO C, PIN 1</description>
                  <value>14</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>GPIOC_6</name>
                  <description>GPIO C, PIN 2</description>
                  <value>15</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- CAPEDGE: Capture Edge -->
            <field>
              <name>CAPEDGE</name>
              <description>Capture Edge, select which Edge should result in a counter increment or decrement</description>
              <bitRange>[17:16]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>RISING</name>
                  <description>Only rising edges result in a counter increment or decrement</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>FALLING</name>
                  <description>Only falling edges  result in a counter increment or decrement</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>BOTH</name>
                  <description>Rising and falling edges result in a counter increment or decrement</description>
                  <value>2</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- TRGEXT: Triggers an other Peripheral -->
            <field>
              <name>TRGEXT</name>
              <description>Triggers an other Peripheral</description>
              <bitRange>[21:20]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>NONE</name>
                  <description>No Trigger is emitted</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>DMA1</name>
                  <description>DMA Controller 1 is triggered, dependant on MODE</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>DMA2</name>
                  <description>DMA Controller 2 is triggered, dependant on MODE</description>
                  <value>2</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>UART</name>
                  <description>UART is triggered, dependant on MODE</description>
                  <value>3</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- Reload: Selects Reload Register n -->
            <field>
              <name>RELOAD</name>
              <description>Select RELOAD Register n to reload Timer on condition</description>
              <bitRange>[25:24]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>RELOAD0</name>
                  <description>Selects Reload Register number 0</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>RELOAD1</name>
                  <description>Selects Reload Register number 1</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>RELOAD2</name>
                  <description>Selects Reload Register number 2</description>
                  <value>2</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>RELOAD3</name>
                  <description>Selects Reload Register number 3</description>
                  <value>3</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- IDR: Inc or dec Reload Register Selection -->
            <field>
              <name>IDR</name>
              <description>Selects, if Reload Register number is incremented, decremented or not modified</description>
              <bitRange>[27:26]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>KEEP</name>
                  <description>Reload Register number does not change automatically</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>INCREMENT</name>
                  <description>Reload Register number is incremented on each match</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>DECREMENT</name>
                  <description>Reload Register number is decremented on each match</description>
                  <value>2</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- START: Starts / Stops the Timer/Counter -->
            <field>
              <name>S</name>
              <description>Starts and Stops the Timer / Counter</description>
              <bitRange>[31:31]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>STOP</name>
                  <description>Timer / Counter is stopped</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>START</name>
                  <description>Timer / Counter is started</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>
          </fields>
        </register>

        <!-- SR: Status Register -->
        <register>
          <name>SR</name>
          <description>Status Register</description>
          <addressOffset>0x04</addressOffset>
          <size>16</size>
          <access>read-write</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0xD701</resetMask>

          <fields>
            <!-- RUN: Shows if Timer is running -->
            <field>
              <name>RUN</name>
              <description>Shows if Timer is running or not</description>
              <bitRange>[0:0]</bitRange>
              <access>read-only</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Stopped</name>
                  <description>Timer is not running</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Running</name>
                  <description>Timer is running</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- MATCH: Shows if a Match was hit -->
            <field>
              <name>MATCH</name>
              <description>Shows if the MATCH was hit</description>
              <bitRange>[8:8]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>No_Match</name>
                  <description>The MATCH condition was not hit</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Match_Hit</name>
                  <description>The MATCH condition was hit</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- UN: Shows if an underflow occured -->
            <field>
              <name>UN</name>
              <description>Shows if an underflow occured. This flag is sticky</description>
              <bitRange>[9:9]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>No_Underflow</name>
                  <description>No underflow occured since last clear</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Underflow</name>
                  <description>A minimum of one underflow occured since last clear</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- OV: Shows if an overflow occured -->
            <field>
              <name>OV</name>
              <description>Shows if an overflow occured. This flag is sticky</description>
              <bitRange>[10:10]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>No_Overflow</name>
                  <description>No overflow occured since last clear</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Overflow_occured</name>
                  <description>A minimum of one overflow occured since last clear</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- RST: Shows if Timer is in RESET state -->
            <field>
              <name>RST</name>
              <description>Shows if Timer is in RESET state</description>
              <bitRange>[12:12]</bitRange>
              <access>read-only</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Ready</name>
                  <description>Timer is not in RESET state and can operate</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>In_Reset</name>
                  <description>Timer is in RESET state and can not operate</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- RELOAD: Shows the currently active Reload Register -->
            <field>
              <name>RELOAD</name>
              <description>Shows the currently active RELOAD Register</description>
              <bitRange>[15:14]</bitRange>
              <access>read-only</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>RELOAD0</name>
                  <description>Reload Register number 0 is active</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>RELOAD1</name>
                  <description>Reload Register number 1 is active</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>RELOAD2</name>
                  <description>Reload Register number 2 is active</description>
                  <value>2</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>RELOAD3</name>
                  <description>Reload Register number 3 is active</description>
                  <value>3</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>
          </fields>
        </register>

        <!-- INT: Interrupt Register -->
        <register>
          <name>INT</name>
          <description>Interrupt Register</description>
          <addressOffset>0x10</addressOffset>
          <size>16</size>
          <access>read-write</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0x0771</resetMask>

          <fields>
            <!-- EN: Interrupt Enable -->
            <field>
              <name>EN</name>
              <description>Interrupt Enable</description>
              <bitRange>[0:0]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Disabled</name>
                  <description>Timer does not generate Interrupts</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Enable</name>
                  <description>Timer triggers the TIMERn Interrupt</description>
                  <value>1</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>

            <!-- MODE: Interrupt Mode -->
            <field>
              <name>MODE</name>
              <description>Interrupt Mode, selects on which condition the Timer should generate an Interrupt</description>
              <bitRange>[6:4]</bitRange>
              <access>read-write</access>
              <enumeratedValues>
                <enumeratedValue>
                  <name>Match</name>
                  <description>Timer generates an Interrupt when the MATCH condition is hit</description>
                  <value>0</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Underflow</name>
                  <description>Timer generates an Interrupt when it underflows</description>
                  <value>1</value>
                </enumeratedValue>
                <enumeratedValue>
                  <name>Overflow</name>
                  <description>Timer generates an Interrupt when it overflows</description>
                  <value>2</value>
                </enumeratedValue>
              </enumeratedValues>
            </field>
          </fields>
        </register>

        <!-- COUNT: Counter Register -->
        <register>
          <name>COUNT</name>
          <description>The Counter Register reflects the actual Value of the Timer/Counter</description>
          <addressOffset>0x20</addressOffset>
          <size>32</size>
          <access>read-write</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0xFFFFFFFF</resetMask>
        </register>

        <!-- MATCH: Match Register -->
        <register>
          <name>MATCH</name>
          <description>The Match Register stores the compare Value for the MATCH condition</description>
          <addressOffset>0x24</addressOffset>
          <size>32</size>
          <access>read-write</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0xFFFFFFFF</resetMask>
        </register>
        
        <!-- PRESCALE: Prescale Read Register -->
        <register>
          <name>PRESCALE_RD</name>
          <description>The Prescale Register stores the Value for the prescaler. The cont event gets divided by this value</description>
          <addressOffset>0x28</addressOffset>
          <size>32</size>
          <access>read-only</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0xFFFFFFFF</resetMask>
        </register>
        
        <!-- PRESCALE: Prescale Write Register -->
        <register>
          <name>PRESCALE_WR</name>
          <description>The Prescale Register stores the Value for the prescaler. The cont event gets divided by this value</description>
          <addressOffset>0x28</addressOffset>
          <size>32</size>
          <access>write-only</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0xFFFFFFFF</resetMask>
        </register>


        <!-- RELOAD: Array of Reload Register with 4 elements-->
        <register>
          <dim>4</dim>
          <dimIncrement>4</dimIncrement>
          <name>RELOAD[%s]</name>
          <description>The Reload Register stores the Value the COUNT Register gets reloaded on a when a condition was met.</description>
          <addressOffset>0x50</addressOffset>
          <size>32</size>
          <access>read-write</access>
          <resetValue>0x00000000</resetValue>
          <resetMask>0xFFFFFFFF</resetMask>
        </register>
      </registers>
    </peripheral>

    <!-- Timer 1 -->
    <peripheral derivedFrom="TIMER0">
      <name>TIMER1</name>
      <baseAddress>0x40010100</baseAddress>
      <interrupt>
        <name>TIMER1</name>
        <description>Timer 2 interrupt</description>
        <value>4</value>
      </interrupt>
    </peripheral>

    <!-- Timer 2 -->
    <peripheral derivedFrom="TIMER0">
      <name>TIMER2</name>
      <baseAddress>0x40010200</baseAddress>
      <interrupt>
        <name>TIMER2</name>
        <description>Timer 2 interrupt</description>
        <value>6</value>
      </interrupt>
    </peripheral>
  </peripherals>
</device>
```
  </details>
  - [SVD File Example](https://www.keil.com/pack/doc/CMSIS_Dev/SVD/html/svd_Example_pg.html) 
</details>

### 3） 生成PACK
<details>

#### 3.1）gen_pack.sh脚本的使用 

</details>

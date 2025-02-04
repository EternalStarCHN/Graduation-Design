# 介绍
 “聪明的加湿器”是一款基于STM32单片机和Esp8266实现的智能加湿器，它与互联网相连接，可以使用天猫精灵等智能音箱或手机App进行运行状态的控制。

 它主要由**硬件**和**软件**两个部分构成，可以通过修改相应电路和代码实现功能的更新。
 # 硬件
 硬件部分的原理图与PCB均使用[Altium Designer 19](https://www.altium.com.cn/)设计和绘制，原理图库和PCB库文件中包含了一些该项目使用的不常用器件的原理图和封装。

 “聪明的加湿器”的硬件部分主要可以分为以下部分：
 1. 单片机控制电路
 2. 锂电池电源电路
 3. 超声波加湿电路
 4. 传感器模块
 5. 显示屏模块
 6. 网络模块  
 . . .
 
 主要介绍*1-3*部分

 ## 单片机控制电路
 **该电路即修改后的STM32F103C8T6单片机的最小核心电路**

 ## 锂电池电源电路
 该电路用于*单节3.7v锂电池*的充放电管理以及升压至5V  
 | 实现功能 | 所使用的芯片 |
 | :--- | :--- |
 | 锂电池充放电管理 | TC4056芯片 | 
 | 3.7V锂电池升压至5V | TC6291C芯片 |  

 （👆这两部分也可以使用TP5400芯片一起实现，不过TP5400的电路有一个电解电容不方便放进外壳中，参考我的[LiTestPCB](https://github.com/EternalStarCHN/LiTestPCB)仓库）  

 使用三极管制作了供电自动切换电路，实现了边充边放等功能
 * 在micro-usb口有外接电源时，VCC5V输出VBUS
 * 在micro-usb口无外接电源时，VCC5V输出由3.7V锂电池升压后的5V电压
 ## 超声波加湿电路
 本次设计的超声波加湿电路基于深圳芯科泰半导体公司推出的XKT-201芯片，工作电压为 5V，在特殊需求下，可以直接使用 4.2V 锂电池直接为发射部分提供电源，但是要同时注意修改驱动频率。芯片采用 SOP-8 封装，尺寸相比于传统方案得以进一步压缩。外围器件使用数量少，使成品尺寸进一步压缩，生产工艺和成本得到进一步优化。芯片设计工作频率为 108KHz。芯片预留了水位监测控制脚（8 脚），**可实现枯水断电**，以保护雾化片不会因为缺水而干烧。     

 元器件使用需注意： 
 | 元器件编号 | 规格 |
 | :--- | :--- |
 | C31 | X7R |
 | C32 | X7R | 
 | C33 | NPO |  

 实际应用中，调整芯片1 脚 2 脚所串电阻可进行频率调节，由于 PCB 的布板可能导致整体频率偏差，可以通过修改他的阻值，将频率修改到工作频率 108KHz，同时**超声波加湿电路的稳定工作离不开稳定的电源供电**。   

 # 软件
 ## 单片机程序
 单片机程序使用**C语言**编写，调试烧录环境为**Keil5**，用于实现各个外设的正常工作以及加湿器的多种运行模式。

 ## ESP8266程序
 加湿器与互联网之间的连接主要依靠[**巴法云**](https://bemfa.com/)物联网平台，它拥有TCP设备云、TCP创客云、MQTT设备云和图云四种不同的协议。

 ESP8266的程序基于**arduino**进行开发，官网下载网址：`https://www.arduino.cc/en/Main/Software`

 在ESP8266的代码中，可以通过修改UID、TOPIC、DEFAULT_STASSID和DEFAULT_STAPSW的值实现用户、主题、WIFI账号、WIFI密码的修改。

 ```Arduino
//WIFI名称，区分大小写，不要写错
#define DEFAULT_STASSID  "WIFI名称"
//WIFI密码
#define DEFAULT_STAPSW   "WIFI密码"
//用户私钥，可在控制台获取,修改为自己的UID
#define UID  "你的私钥"
//主题名字，可在控制台新建
#define TOPIC  "你的主题"
//单片机引脚值
const int Hum_Pin = 0;//加湿器控制引脚(LOW:停止;HIGH:运行)
const int Con_Pin = 2;//模块联网状态(LOW:连接;HIGH:断开)
```
 ___
 ## APP程序
 控制“聪明的加湿器”所使用的APP是基于**APP INVENTOR**编写的，国内网址：`http://app.gzjkw.net/`  
 与ESP模块的代码相似，通过修改APP代码中的值即可实现与自己设备的连接。
# 许可证
Smart Humidifier (Graduation Design) is licensed under [GNU General Public License v3.0](https://github.com/EternalStarCHN/Graduation-Design/blob/master/LICENSE) .

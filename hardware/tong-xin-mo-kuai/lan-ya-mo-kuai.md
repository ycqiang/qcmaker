# 蓝牙模块

![](../../.gitbook/assets/ying-jian-1215876.png)

## 5.3.1 简介

蓝牙是一种支持设备短距离通信的无线电技术，其传输距离一般在 10 米以内。本说明书中将介绍 HC-05蓝牙模块，该模块是基于 Bluetooth Specification V2.0 带 EDR 蓝牙协议的数传串口模块。本模块支持UART,USB,SPI,PCM,SPDIF等接口，并支持SPP蓝牙串口协议，具有成本低、体积小、功耗低、收发灵敏性高等优点，只需配备少许的外围元件就能实现其强大功能。

HC-05 蓝牙模块有以下引脚：

state：主机清除记忆，低电平有效，在实际使用中不必连接该引脚，空出即可。

TXD：发送端，连接到主控板的 RXD 端口（程序中需设定 RXD 的端口号）。

RXD：接收端，连接到主控板的 TXD 端口（程序中需设定 TXD 的端口号）。

GND：负极，连接到主控板的 G 端口。

VCC：正极，连接到主控板的 V 端口。

EN:使能引脚，低电平有效，在实际使用中不必连接该引脚，空出即可。

注：

1.TXD、RXD 为串口通信的数据传输引脚，正常通信时要交叉连接，即设备自身的 TXD 连接另一设备的 RXD，设备自身的 RXD 连接另一设备的 TXD。另外，设备自身的 TXD 和 RXD 也可以连接在一起，实现自收自发功能，可用来测试设备本身的收发功能是否正常。

2.本说明书只介绍在 Mixly 编程软件中设置蓝牙模块的参数，不涉及使用串口调试助手设置蓝牙模块参数。

3.示例中使用主控板的软串口\(D10、D11 或者其他可做软串口的端口\)连接蓝牙模块，不采用 D0 和 D1 硬串口。

## 5.3.2 蓝牙模块使用示例

实现功能：串口监视器发送的数据能在串口调试助手端看到。

### 1.AT模式设置

在连接蓝牙的时候，要注意线的连接是否正确，检查清楚再进行通电。

#### 1）Arduino HC05 AT模式接线

Arduino 5V - VCCArduino GND - GNDArduino Pin10 - TXDArduino Pin11 - RXD

#### 2）Arduino 进入 AT 模式代码

接下来，我们需要为使用 Arduino 设置蓝牙模块 AT 模式编写程序，这个程序是让我们可以通过 Arduino IDE 提供的串口监视器来设置蓝牙模块。

Arduino IDE界面如图5.6-1所示：

![&#x56FE;5.6-1](../../.gitbook/assets/ying-jian-1216875.png)

详细的 Arduino 代码如下：

`#include <SoftwareSerial.h>`

`// Pin10为RX，接HC05的TXD`

`// Pin11为TX，接HC05的RXD`

`SoftwareSerial BT(10, 11);`

`char val;`

`void setup() {`

`Serial.begin(38400);`

`Serial.println("BT is ready!");`

`// HC-05默认，38400`

`BT.begin(38400);`

`}`

`void loop() {`

`if (Serial.available()) {`

`val = Serial.read();`

`BT.print(val);`

`}`

`if (BT.available()) {`

`val = BT.read();`

`Serial.print(val);`

`}`

`}`

#### 3）利用 Arduino IDE 串口监视器进行调试

首先，将 Arduino 断电，然后按着蓝牙模块上的黑色按钮（复位按钮），再让 Arduino 通电，如果蓝牙模块指示灯按2秒的频率闪烁，表明蓝牙模块已经正确进入 AT 模式，此时松开黑色按钮。

打开 Arduino IDE 的串口监视器，选择正确的端口，将输出格式设置为 Both: NL & CR ，波特率设置为 38400 ，可以看到串口监视器中显示 BT is ready! 的信息。

然后，输入 AT ，如果一切正常，串口显示器会显示 OK。

接下来，我们即可对蓝牙模块进行设置，常用 AT 命令如下：（注意要英文输入）

AT+ORGL \# 恢复出厂模式

AT+NAME=&lt;Name&gt;\# 设置蓝牙名称

AT+ROLE=0 \# 设置蓝牙为从模式

AT+ROLE=1 \# 设置蓝牙为主模式

AT+CMODE=1 \# 设置蓝牙为任意设备连接模式

AT+PSWD=&lt;Pwd&gt;\# 设置蓝牙匹配密码

正常情况下，命令发送后，会返回 OK ，如果没有返回任何信息，请检查接线是否正确，蓝牙模块是否已经进入 AT 模式，如果上述两点都没有问题，可能是蓝牙模块的问题，可以找蓝牙模块供应商咨询。

设置完毕后，断开电源，再次通电，这时，蓝牙模块指示灯会快速闪烁，这表明蓝牙已经进入正常工作模式，两个模块会自动配对然后连接，状态灯会出现慢闪烁指示。

### 2.蓝牙与PC间数据传输

如果要对蓝牙与PC间进行通信，需要将蓝牙设备在AT模式时设置为从模式，AT模式设置好之后蓝牙模块进入正常工作模式，将PC连接蓝牙模块连接。

#### 1）上传程序

硬件连接：蓝牙TX--甜橙D10；蓝牙RX---甜橙D11。

编写程序如下图5.6-2：

![&#x56FE;5.6-2](../../.gitbook/assets/ying-jian-1218011.png)

也可以用代码编程使用Arduino IDE来发送数据，代码如下：

`#include <SoftwareSerial.h>`

`volatile char s;`

`SoftwareSerial mySerial(10,11);`

`void setup(){`

`s = 0;`

`Serial.begin(9600);`

`mySerial.begin(9600);`

`}`

`void loop(){`

`if (mySerial.available() > 0) {`

`s = mySerial.read();`

`Serial.print(s);`

`}`

`}`

#### 2）打开电脑蓝牙

首先打开电脑的 设置，如图5.6-3所示，点击设备--打开蓝牙，单击添加蓝牙或其他设备，如图5.6-4所示：

![&#x56FE;5.6-3](../../.gitbook/assets/ying-jian-1218365.png)

![&#x56FE;5.6-4](../../.gitbook/assets/ying-jian-1218374.png)

#### 3）将PC蓝牙与蓝牙模块进行配对连接

A、点击“蓝牙”，如图5.6-5所示，进入添加设备页面，点击QC-HC05，进行配对连接，如图5.6-6所示，当出现如图5.6-7所示时，PC已经与蓝牙模块连接完成；

B、点击图5.6-4所示的“更多蓝牙选项”，单击“COM端口”如图5.6-8所示，观察传出端口，图中为COM8。

![&#x56FE;5.6-5](../../.gitbook/assets/ying-jian-1218543.png)

![&#x56FE;5.6-6](../../.gitbook/assets/ying-jian-1218552.png)

![&#x56FE;5.6-7](../../.gitbook/assets/ying-jian-1218561.png)

![&#x56FE;5.6-8](../../.gitbook/assets/ying-jian-1218570.png)

#### 4）串口调试助手观察通信

A、打开串口调试助手，界面如图5.6-9所示，修改左侧串口、波特率等设置，观察左下角的COM口状态是否为关闭状态，若不是，点击菜单栏的“停止”按钮，使端口停止工作，再点击圈住的“开关”按钮，打开串口界面如图5.6-10所示，观察从串口监视器发送来的数据。

![&#x56FE;5.6-9](../../.gitbook/assets/ying-jian-1218720.png)

![&#x56FE;5.6-10](../../.gitbook/assets/ying-jian-1218729.png)

## 5.3.3 蓝牙模块应用场景举例

蓝牙模块可实现一对一无线传输数据，既可以在蓝牙模块之间通信，也可以与手机蓝牙建立连接，通过手机 App 与蓝牙模块传输数据。

应用举例：蓝牙遥控车、蓝牙遥控灯、环境监测App 等。


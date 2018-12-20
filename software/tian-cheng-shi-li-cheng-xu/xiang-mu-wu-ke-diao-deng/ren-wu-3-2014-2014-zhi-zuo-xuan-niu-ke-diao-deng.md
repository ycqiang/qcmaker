### 任务3——制作旋钮可调灯

这里，我们用到了一个新的元件——模拟角度电位器，也叫 “滑动变阻器”。通过调节旋钮，可以改变它接入电路的阻值大小。将其连到主控板支持模拟输入的接口上，就可以把阻值作为模拟信号输入到主控板上。主控板根据输入值的大小，确定输出的值(在这里，输入值大，输出值也大;也可能另外一些程序希望输出值随着输入值变大而减小。)

#### 1、任务目标

按下按钮开关，灯被点亮，此时可以使用旋钮调节LED灯的亮度强弱，当单色灯点亮时再次按下按钮，灯熄灭。

#### 2、流程图

![img](/assets/image263.jpg)

图3.5-10

#### 3、程序编程

需要注意的是，主控板支持的模拟输入信号的大小范围是 0~1023。然而，模拟输出大小是 0~255。因此，模拟输入的数值，不能直接进行模拟输出，我们需要一种办法，能够把 0~1023 内的数，按比例缩小，转化成 0~255 之间的数，再模拟输出。方法如下:

![img](/assets/image265.gif)

图3.5-11

![img](/assets/image267.jpg)

图3.5-12

#### 4、硬件连接

硬件连接：LED ——10；模拟角度电位器——A0；按钮——2。注意插线时的颜色对应。

![img](/assets/image269.jpg)

图3.5-13

#### 5、Q&A

Q：旋钮从最小阻值到最大阻值，经历了四次亮灭，为什么？

A：查看程序是否使用的“映射”程序块，而不是“约束”程序块，其次查看映射的取值范围，模拟输入的范围为0-1023，输出的范围为0-255。

#### 6、拓展

1、知识点总结

1）旋钮的原理，与滑动变阻器类似；

2）“映射”程序块，表达一种输出的对应关系；

3）中断，切换state变量的状态，同时切换灯的状态；

4）条件结构；

2、相关案例

1）本项目任务1；

2）本项目任务2；
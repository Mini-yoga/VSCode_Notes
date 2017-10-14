![](resources/857722A5C1C027ED229D59066506E12B.jpg)

首先try，使用try若是抛出异常，不会转到else中的异常，而是try的抛出异常，与我们想要的在else中写代码想法不符

![](resources/6C6F534887AC9D13F2553A8C6D5F7BB3.jpg)

使用try? 就达成我们的目的啦，如果有异常会进入else处理，但是，try?会给结果再加一个optiional，还需要再解包！
`1、列举命令：help print` 或者 `help thread`

2、调试时改变一个值：expression count = 10

3、打印：print 为输出 = **expression —-（“—-”指表示结束以及输入的开始） p 16 = e —- 16**

**4、以对象为单位打印：****e -o —- XXX = po XXX**

**5、打印二进制，十六进制，字符…：p/x 16**

**6、声明变量：e int $a = 2**

**7、当前行数和源码文件：**frame info（`help frame`，`help thread` 和 `help process获取更多`

`信息）`

`8、控制程序流程：`thread return 伪造返回值，放在开头，可伪造函数返回值

9、断点管理：`breakpoint list` (或者 `br li`) 在使用 `breakpoint enable <breakpointID>` 和 `breakpoint disable <breakpointID>`

**10、创建符号断点，编辑一定条件断点，expression断点（UI上）**

**11、断点行为：定义断点时候的操作，可有语音提示**

**12、赋值后继续运行**![](resources/EA3D182BCCAFE70F577F99B2F33A3201.jpg)

13、申请查看字节，做完要释放内存

**14、查看内存：**x/4c $str

15、更新UI,PUSH ViewController

……

其他文献：

<http://lldb.llvm.org/tutorial.html>

[https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb\_to\_lldb\_transition\_guide/document/Introduction.html](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/Introduction.html)

<http://objccn.io/issue-19-2/>

<http://www.starfelix.com/blog/2014/03/17/lldbdiao-shi-ming-ling-chu-tan/>


1、RCC：resource compiler，Qt 提供了一个比较好的机制， QtResource。 使用rcc 把文件保存到 .rcc 文件， 然后使用 qrc 指定 rcc 结构。

1）qrc 和 rcc 放在同一个目录下。命令一般使用： rcc -binary source.qml -o target.rcc

2）添加新文件或者删除文件, 或者进行了其它修改了目录树的操作时， 需要重新生成rcc文件； 文件内容改变则不需要重新编译生成rcc文件。——但是它们都需要重新生成 qrc.cpp 以及 qrc.o 文件。

3）在qml中使用rcc的内容， 前缀是 qrc:/ ; 在 Qt 中使用， 前缀是 :

4）在 .qrc 文件中， 可以使用别名简化编码操作——对于那些使用频率比较高的资源。

5）使用 QResource::registerResource 注册rcc文件
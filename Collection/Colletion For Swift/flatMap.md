今天看了一篇关于flatMap的帖子，可用于去除数组中的nil元素，但是精髓还是不太懂额，看来要从MAP学起了。

### **flatMap 与 map 不同之处是**

 flatMap返回后的数组中不存在 nil 同时它会把Optional解包;

 flatMap 还能把数组中存有数组的数组 一同打开变成一个新的数组 ;

 flatMap也能把两个不同的数组合并成一个数组 这个合并的数组元素个数是前面两个数组元素个数的乘积

<http://swift.gg/2015/08/06/swift-2-flatmap/>
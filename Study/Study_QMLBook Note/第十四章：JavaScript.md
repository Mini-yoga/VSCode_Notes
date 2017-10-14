JavaScript基于node js也开始引导web服务器的开发。因此它使非常适合在声明式QML语言上添加的命令性语言。QML本身作为一个申明式语言用于表达用户界面层次，但是这仅限于表达操作。有时你需要一个方法表达业务，使用JavaScript来完成。
<font color="#4590a3" size="2px">注意：在Qt社区有一个开放性的问题是在目前Qt程序中关于混合使用QML/JS/QtC++的正确性。通常建议的混合方式是在你的应用程序中将JS部分限制在最小，将你的业务逻辑部分放在QtC++中，UI逻辑放在QML/JS中。</font>

```js
import "util.js" as Util // import a pure JS module

Button {
  width: 200
  height: width*2 // JS on the right side of property binding

  // standalone function (not really useful)
  function log(msg) {
    console.log("Button> " + msg);
  }

  onTriggered: {
    // this is JavaScript
    log();
    Qt.quit();
  }
}
```

（1）浏览器/HTML与QtQuick/QML对比

在HTML中，你只能在事件操作（event handlers），例如页面加载（page loaded），鼠标点击（mouse pressed）中添加JS。例如通常在页面加载中初始化你的JS，这在QML中与组件加载完成（Component.onCompleted）类似。例如你不能使用JS来绑定属性（至少不是直接绑定，AngularJS增强了DOM树允许这种操作，但这和典型HTML相去甚远）

（2）语法

1)JS有函数作用域，没有C++中的块作用域

2)switch相对于C++中只可以切换整数值，JavaScript可以切换其它类型的值

（3）JS对象

在使用JS工作时，有一些对象和方法会被频繁的使用。下面是它们的一个小的集合。

* Math.floor(v),Math.ceil(v),Math.round(v) - 从浮点数获取最大，最小和随机整数
* Math.random() - 创建一个在0到1之间的随机数
* Object.keys(o) - 获取对象的索引值（包括QObject）
* JSON.parse(s), JSON.stringify(o) - 转换在JS对象和JSON字符串
* Number.toFixed(p) - 修正浮点数精度
* Date - 日期时间操作
* <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference>
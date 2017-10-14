* 消除浏览器差异：你不需要自己写冗长的代码来针对不同的浏览器来绑定事件，编写AJAX等代码；
* 简洁的操作DOM的方法：写`$('#test')`肯定比`document.getElementById('test')`来得简洁；
* 轻松实现动画、修改CSS等各种操作。

  $符号

`$`是著名的jQuery符号。实际上，jQuery把所有功能全部封装在一个全局变量`jQuery`中，而`$`也是一个合法的变量名，它是变量`jQuery`的别名。

jQuery在占用`$`之前，先在内部保存了原来的`$`,调用`jQuery.noConflict()`时会把原来保存的变量还原。



**选择器：jQuery的选择器就是帮助我们快速定位到一个或多个DOM节点**
---------------------------------------

### 按ID查找

如果某个DOM节点有`id`属性，利用jQuery查找如下：

```
// 查找\<div id="abc"\>:
var div = $('#abc’);
```

    *注意*，#abc以#开头。返回的对象是jQuery对象。

jQuery对象类似数组，它的每个元素都是一个引用了DOM节点的对象。

如果你拿到了一个DOM对象，那可以简单地调用`$(aDomObject)`把它变成jQuery对象，这样就可以方便地使用jQuery的API了。

### 按tag查找

按tag查找只需要写上tag名称就可以了：

```
var ps = $('p'); // 返回所有\<p\>节点
ps.length; // 数一数页面有多少个\<p\>节点 

```

按class查找

按class查找注意在class名称前加一个`.`：

```
var a = $('.red'); // 所有节点包含`class="red”`都将返回
```

```
// 例如:
// \<div class="red"\>...\</div\>
// \<p class="green red"\>...\</p\>
```

    var a = $('.red.green'); // 注意没有空格！

```
// 符合条件的节点：
// \<div class="red green"\>...\</div\>
// \<div class="blue green red"\>...\</div\>
```

### 按属性查找

一个DOM节点除了`id`和`class`外还可以有很多属性，很多时候按属性查找会非常方便，比如在一个表单中按属性来查找：

```
var email = $('[name=email]'); // 找出\<??? name="email"\> var passwordInput = $('[type=password]'); // 找出\<??? type="password"\> var a = $('[items="A B"]'); // 找出\<??? items="A B"\>按属性查找还可以使用前缀查找或者后缀查找：
```

```
var icons = $('[name^=icon]'); // 找出所有name属性值以icon开头的DOM
// 例如: name="icon-1", name="icon-2"
var names = $('[name$=with]'); // 找出所有name属性值以with结尾的DOM
// 例如: name="startswith", name="endswith"
```

### 组合查找

组合查找就是把上述简单选择器组合起来使用。如果我们查找`$('[name=email]')`，很可能把表单外的`<div name="email">`也找出来，但我们只希望查找`<input>`，就可以这么写：

```
var emailInput = $('input[name=email]'); // 不会找出\<div name="email"\>

```

同样的，根据tag和class来组合查找也很常见：

```
var tr = $('tr.red'); // 找出\<tr class="red ..."\>...\</tr\>

```

```

### 多项选择器

多项选择器就是把多个选择器用`,`组合起来一块选：

```
$('p,div'); // 把\<p\>和\<div\>都选出来
$('p.red,p.green'); // 把\<p class="red"\>和\<p class="green"\>都选出来

```

```

```

```

**层级选择器:如果两个DOM元素具有层级关系，就可以用`$('ancestor descendant')`来选择，层级之间用空格隔开。**

\<!-- HTML结构 --\>
-----------------

```
\<div class="testing"\>
    \<ul class="lang"\>
        \<li class="lang-javascript"\>JavaScript\</li\>
        \<li class="lang-python"\>Python\</li\>
        \<li class="lang-lua"\>Lua\</li\>
    \</ul\>
\</div\>

```

要选出JavaScript，可以用层级选择器：

```
$('ul.lang li.lang-javascript'); // [\<li class="lang-javascript"\>JavaScript\</li\>]
$('div.testing li.lang-javascript'); // [\<li class="lang-javascript"\>JavaScript\</li\>]

```

因为`<div>`和`<ul>`都是`<li>`的祖先节点，所以上面两种方式都可以选出相应的`<li>`节点。

要选择所有的`<li>`节点，用：

```
$('ul.lang li');
```

### 子选择器（Child Selector）

子选择器`$('parent>child')`类似层级选择器，但是限定了层级关系必须是父子关系，就是`<child>`节点必须是`<parent>`节点的直属子节点。

### 过滤器（Filter）

过滤器一般不单独使用，它通常附加在选择器上，帮助我们更精确地定位元素。观察过滤器的效果：

```
$('ul.lang li'); // 选出JavaScript、Python和Lua 3个节点

$('ul.lang li:first-child'); // 仅选出JavaScript
$('ul.lang li:last-child'); // 仅选出Lua
$('ul.lang li:nth-child(2)'); // 选出第N个元素，N从1开始
$('ul.lang li:nth-child(even)'); // 选出序号为偶数的元素
$('ul.lang li:nth-child(odd)'); // 选出序号为奇数的元素
```

### 表单相关

针对表单元素，jQuery还有一组特殊的选择器：

* `:input`：可以选择`<input>`，`<textarea>`，`<select>`和`<button>`；
* `:file`：可以选择`<input type="file">`，和`input[type=file]`一样；
* `:checkbox`：可以选择复选框，和`input[type=checkbox]`一样；
* `:radio`：可以选择单选框，和`input[type=radio]`一样；
* `:focus`：可以选择当前输入焦点的元素，例如把光标放到一个`<input>`上，用`$('input:focus')`就可以选出；
* `:checked`：选择当前勾上的单选框和复选框，用这个选择器可以立刻获得用户选择的项目，如`$('input[type=radio]:checked')`；
* `:enabled`：可以选择可以正常输入的`<input>`、`<select>` 等，也就是没有灰掉的输入；
* `:disabled`：和`:enabled`正好相反，选择那些不能输入的。

**查找和过滤:通常情况下选择器可以直接定位到我们想要的元素，但是，当我们拿到一个jQuery对象后，还可以以这个对象为基准，进行查找和过滤。**

用`find()`查找：

```
var ul = $('ul.lang'); // 获得\<ul\>
var dy = ul.find('.dy'); // 获得JavaScript, Python, Scheme
var swf = ul.find('\#swift'); // 获得Swift
var hsk = ul.find('[name=haskell]'); // 获得Haskell

```

如果要从当前节点开始向上查找，使用`parent()`方法：

```
var swf = $('\#swift'); // 获得Swift
var parent = swf.parent(); // 获得Swift的上层节点\<ul\>
var a = swf.parent('div.red'); // 从Swift的父节点开始向上查找，直到找到某个符合条件的节点并返回

```

对于位于同一层级的节点，可以通过`next()`和`prev()`方法，例如：

当我们已经拿到`Swift`节点后：

```
var swift = $('\#swift');

swift.next(); // Scheme
swift.next('[name=haskell]'); // Haskell，因为Haskell是后续第一个符合选择器条件的节点

swift.prev(); // Python
swift.prev('.js'); // JavaScript，因为JavaScript是往前第一个符合选择器条件的节点

```

```

`filter()`方法可以过滤掉不符合选择器条件的节点：

```
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var a = langs.filter('.dy'); // 拿到JavaScript, Python, Scheme

```

或者传入一个函数，要特别注意函数内部的`this`被绑定为DOM对象，不是jQuery对象：

```
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
langs.filter(function () {
    return this.innerHTML.indexOf('S') === 0; // 返回S开头的节点
}); // 拿到Swift, Scheme

```

`map()`方法把一个jQuery对象包含的若干DOM节点转化为其他对象：

```
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var arr = langs.map(function () {
    return this.innerHTML;
}).get(); // 用get()拿到包含string的Array：['JavaScript', 'Python', 'Swift', 'Scheme', 'Haskell']

```

此外，一个jQuery对象如果包含了不止一个DOM节点，`first()`、`last()`和`slice()`方法可以返回一个新的jQuery对象，把不需要的DOM节点去掉：

```
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var js = langs.first(); // JavaScript，相当于$('ul.lang li:first-child')
var haskell = langs.last(); // Haskell, 相当于$('ul.lang li:last-child')
var sub = langs.slice(2, 4); // Swift, Scheme, 参数和数组的slice()方法一致

```

```

```

```

### 修改Text和HTML

分别获取文本和HTML：

```
$('\#test-ul li[name=book]').text(); // 'Java & JavaScript'
$('\#test-ul li[name=book]').html(); // 'Java &amp; JavaScript'
```

### 修改CSS

要高亮显示动态语言，调用jQuery对象的`css('name', 'value')`方法，我们用一行语句实现：$('\#test-css li.dy\>span').css('background-color', '\#ffd351').css('color', 'red’);

jQuery对象的所有方法都返回一个jQuery对象（可能是新的也可能是自身），这样我们可以进行链式调用，非常方便。

jQuery对象的`css()`方法可以这么用：

```
var div = $('\#test-div');
div.css('color'); // '\#000033', 获取CSS属性
div.css('color', '\#336699'); // 设置CSS属性
div.css('color', ''); // 清除CSS属性
```

=

显示和隐藏DOM
========

要隐藏一个DOM，我们可以设置CSS的`display`属性为`none`，利用`css()`方法就可以实现。不过，要显示这个DOM就需要恢复原有的`display`属性，这就得先记下来原有的`display`属性到底是`block`还是`inline`还是别的值。

考虑到显示和隐藏DOM元素使用非常普遍，jQuery直接提供`show()`和`hide()`方法，我们不用关心它是如何修改`display`属性的，总之它能正常工作

### 操作表单

对于表单元素，jQuery对象统一提供`val()`方法获取和设置对应的`value`属性：

### 添加DOM

要添加新的DOM节点，除了通过jQuery的`html()`这种暴力方法外，还可以用`append()`方法。`append()`把DOM添加到最后，`prepend()`则把DOM添加到最前。

另外注意，如果要添加的DOM节点已经存在于HTML文档中，它会首先从文档移除，然后再添加，也就是说，用`append()`，你可以移动一个DOM节点

### 删除节点

要删除DOM节点，拿到jQuery对象后直接调用`remove()`方法就可以了。如果jQuery对象包含若干DOM节点，实际上可以一次删除多个DOM节点：





事件
==

#### 

jQuery能够绑定的事件主要包括：

### 鼠标事件

#### 

click: 鼠标单击时触发； dblclick：鼠标双击时触发； mouseenter：鼠标进入时触发； mouseleave：鼠标移出时触发； mousemove：鼠标在DOM内部移动时触发； hover：鼠标进入和退出时触发两个函数，相当于mouseenter加上mouseleave。

### 键盘事件

#### 

键盘事件仅作用在当前焦点的DOM上，通常是`<input>`和`<textarea>`。

keydown：键盘按下时触发； keyup：键盘松开时触发； keypress：按一次键后触发。

### 其他事件

#### 

focus：当DOM获得焦点时触发； blur：当DOM失去焦点时触发； change：当`<input>`、`<select>`或`<textarea>`的内容改变时触发； submit：当`<form>`提交时触发； ready：当页面被载入并且DOM树完成初始化后触发。

如果你遇到`$(function () {...})`的形式，牢记这是`document`对象的`ready`事件处理函数。

### 事件参数

#### 

有些事件，如`mousemove`和`keypress`，我们需要获取鼠标位置和按键的值，否则监听这些事件就没什么意义了。所有事件都会传入`Event`对象作为参数，可以从`Event`对象上获取到更多的信息：

### 取消绑定

#### 

一个已被绑定的事件可以解除绑定，通过`off('click', function)`



动画
==

### show / hide

直接以无参数形式调用`show()`和`hide()`，会显示和隐藏DOM元素。但是，只要传递一个时间参数进去，就变成了动画

### slideUp / slideDown

你可能已经看出来了，`show()`和`hide()`是从左上角逐渐展开或收缩的，而`slideUp()`和`slideDown()`则是在垂直方向逐渐展开或收缩的。

### fadeIn / fadeOut

`fadeIn()`和`fadeOut()`的动画效果是淡入淡出，也就是通过不断设置DOM元素的`opacity`属性来实现

### 自定义动画

如果上述动画效果还不能满足你的要求，那就祭出最后大招：`animate()`，它可以实现任意动画效果，我们需要传入的参数就是DOM元素最终的CSS状态和时间，jQuery在时间段内不断调整CSS直到达到我们设定的值

### 串行动画

jQuery的动画效果还可以串行执行，通过`delay()`方法还可以实现暂停

### 为什么有的动画没有效果

你可能会遇到，有的动画如`slideUp()`根本没有效果。这是因为jQuery动画的原理是逐渐改变CSS的值，如`height`从`100px`逐渐变为`0`。但是很多不是block性质的DOM元素，对它们设置`height`根本就不起作用，所以动画也就没有效果。

此外，jQuery也没有实现对`background-color`的动画效果，用`animate()`设置`background-color`也没有效果。这种情况下可以使用CSS3的`transition`实现动画效果。



AJAX
====

### ajax

jQuery在全局对象`jQuery`（也就是`$`）绑定了`ajax()`函数，可以处理AJAX请求。`ajax(url, settings)`函数需要接收一个URL和一个可选的`settings`对象，常用的选项如下：

* async：是否异步执行AJAX请求，默认为`true`，千万不要指定为`false`；
* method：发送的Method，缺省为`'GET'`，可指定为`'POST'`、`'PUT'`等；
* contentType：发送POST请求的格式，默认值为`'application/x-www-form-urlencoded; charset=UTF-8'`，也可以指定为`text/plain`、`application/json`；
* data：发送的数据，可以是字符串、数组或object。如果是GET请求，data将被转换成query附加到URL上，如果是POST请求，根据contentType把data序列化成合适的格式；
* headers：发送的额外的HTTP头，必须是一个object；
* dataType：接收的数据格式，可以指定为`'html'`、`'xml'`、`'json'`、`'text'`等，缺省情况下根据响应的`Content-Type`猜测。

扩展
==

### 编写jQuery插件

给jQuery对象绑定一个新方法是通过扩展`$.fn`对象实现的。让我们来编写第一个扩展——`highlight1()`：

```coffee
$.fn.highlight1 = function () {
    // this已绑定为当前jQuery对象:
    this.css('backgroundColor', '#fffceb').css('color', '#d85030');
    return this;
}
```

为什么最后要`return this;`？因为jQuery对象支持链式操作，我们自己写的扩展方法也要能继续链式下去：

```coffee
$('span.hl').highlight1().slideDown();
```

我们设定的默认值应该能允许用户修改：

```coffee
$.fn.highlight = function (options) {
    // 合并默认值和用户设定值:
    var opts = $.extend({}, $.fn.highlight.defaults, options);
    this.css('backgroundColor', opts.backgroundColor).css('color', opts.color);
    return this;
}

// 设定默认值:
$.fn.highlight.defaults = {
    color: '#d85030',
    backgroundColor: '#fff8de'
}

```


`Array`有`map()`和`filter()`方法，可是Object没有这些方法。直接找一个成熟可靠的第三方开源库，使用统一的函数来实现`map()`、`filter()`这些操作。正如jQuery统一了不同浏览器之间的DOM操作的差异，让我们可以简单地对DOM进行操作，underscore则提供了一套完善的函数式编程的接口，让我们更方便地在JavaScript中实现函数式编程。

jQuery在加载时，会把自身绑定到唯一的全局变量`$`上，underscore与其类似，会把自身绑定到唯一的全局变量`_`上，这也是为啥它的名字叫underscore的原因。

### map/filter

### 

和`Array`的`map()`与`filter()`类似，但是underscore的`map()`和`filter()`可以作用于Object。当作用于Object时，传入的函数为`function (value, key)`，第一个参数接收value，第二个参数接收key：

### every / some

当集合的所有元素都满足条件时，`_.every()`函数返回`true`，当集合的至少一个元素满足条件时，`_.some()`函数返回`true`：

### max / min

这两个函数直接返回集合中最大和最小的数：

### groupBy

`groupBy()`把集合的元素按照key归类，key由传入的函数返回：

### shuffle / sample

`shuffle()`用洗牌算法随机打乱一个集合：

`sample()`则是随机选择一个或多个元素：

Arrays
======

### first / last

顾名思义，这两个函数分别取第一个和最后一个元素：

### flatten

`flatten()`接收一个`Array`，无论这个`Array`里面嵌套了多少个`Array`，`flatten()`最后都把它们变成一个一维数组：

### zip / unzip

`zip()`把两个或多个数组的所有元素按索引对齐，然后按索引合并成新数组。例如，你有一个`Array`保存了名字，另一个`Array`保存了分数，现在，要把名字和分数给对上，用`zip()`轻松实现：

### object

有时候你会想，与其用`zip()`，为啥不把名字和分数直接对应成Object呢？别急，`object()`函数就是干这个的,注意`_.object()`是一个函数，不是JavaScript的`Object`对象。

### range

`range()`让你快速生成一个序列，不再需要用`for`循环实现了：

Functions
=========

#### bind

`bind()`可以帮我们把`console`对象直接绑定在`log()`的`this`指针上，以后调用`log()`就可以直接正常调用了

```coffee
'use strict';

var log = _.bind(console.log, console);
log('Hello, world!');
// 输出Hello, world!
```

### partial

`partial()`就是为一个函数创建偏函数。

### memoize

如果一个函数调用开销很大，我们就可能希望能把结果缓存下来，以便后续调用时直接获得结果。

### once

顾名思义，`once()`保证某个函数执行且仅执行一次。如果你有一个方法叫`register()`，用户在页面上点两个按钮的任何一个都可以执行的话，就可以用`once()`保证函数仅调用一次，无论用户点击多少次：

### delay

`delay()`可以让一个函数延迟执行，效果和`setTimeout()`是一样的



Objects
=======

### keys / allKeys

`keys()`可以非常方便地返回一个object自身所有的key，但不包含从原型链继承下来的

`allKeys()`除了object自身的key，还包含从原型链继承下

### values

和`keys()`类似，`values()`返回object自身但不包含原型链继承的所有值：

### mapObject

`mapObject()`就是针对object的map

`invert()`把object的每个key-value来个交换，key变成value，value变成key

### extend / extendOwn

`extend()`把多个object的key-value合并到第一个object并返回

`extendOwn()`和`extend()`类似，但获取属性时忽略从原型链继承下来的属性。

### clone

如果我们要复制一个object对象，就可以用`clone()`方法，它会把原有对象的所有属性都复制到新的对象中：

### isEqual

`isEqual()`对两个object进行深度比较，如果内容完全相同，则返回`true`：



Chaining
========

underscore提供了把对象包装成能进行链式调用的方法，就是`chain()`函数
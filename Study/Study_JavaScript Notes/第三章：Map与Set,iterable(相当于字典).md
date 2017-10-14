1、Map

`Map`是一组键值对的结构，具有极快的查找速度。

```coffee
var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```



1、Set

`Set`和`Map`类似，也是一组key的集合，但不存储value。

```coffee
var s = new Set([1, 2, 3]);
s.add(4)
s; // Set {1, 2, 3, 4}
s.delete(3);
s; // Set {1, 2, 4}
```

`for ... of`循环和`for ... in`循环有何区别？

`for ... in`循环由于历史遗留问题，它遍历的实际上是对象的属性名称。一个`Array`数组实际上也是一个对象，它的每个元素的索引被视为一个属性。

当我们手动给`Array`对象添加了额外的属性后，`for ... in`循环将带来意想不到的意外效果：

```coffee
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    alert(x); // '0', '1', '2', 'name'
}
```

`for ... in`循环将把`name`包括在内，但`Array`的`length`属性却不包括在内。

`for ... of`循环则完全修复了这些问题，它只循环集合本身的元素：

```coffee
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x of a) {
    alert(x); 'A', 'B', 'C'
}
```

更好的方式是直接使用`iterable`内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。

```coffee
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    alert(element);
});

var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
m.forEach(function (value, key, map) {
    alert(value);
});

var a = ['A', 'B', 'C'];
a.forEach(function (element) {
    alert(element);
});
```
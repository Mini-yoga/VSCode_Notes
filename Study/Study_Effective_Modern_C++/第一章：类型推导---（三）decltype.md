给定一个变量名或者表达式，decltype会告诉你这个变量名或表达式的类型。

1、返回值需要用到参数
此处使用了C++11的尾随返回类型技术，即函数的返回值类型在函数参数之后声明(“->”后边)。尾随返回类型的一个优势是在定义返回值类型的时候使用函数参数。例如在函数authAndAccess中，我们使用了c和i定义返回值类型。在传统的方式下，我们在函数名前面声明返回值类型，c和i是得不到的，因为此时c和i还没被声明。

```js
    template<typename Container, typename Index>    // works, but
    auto authAndAccess(Container& c, Index i)       // requires
      -> decltype(c[i])                             // refinements
    {
      authenticateUser();
      return c[i];
    }
```

C++11允许单语句的lambda表达式的返回类型被推导，编译器将从函数的实现来推导这个函数的返回类型

```js
    template<typename Container, typename Index>         // C++14;
    auto authAndAccess(Container &c, Index i)            // not quite
    {                                                    // correct
      authenticateUser();
      return c[i];
    }                                 // return type deduced from c[i]
```

总结：
1、decltype几乎总是得到一个变量或表达式的类型而不需要任何修改
2、对于非变量名的类型为T的左值表达式，decltype总是返回T&
3、C++14支持decltype(auto)，它的行为就像auto,从初始化操作来推导类型，但是它推导类型时使用decltype的规则
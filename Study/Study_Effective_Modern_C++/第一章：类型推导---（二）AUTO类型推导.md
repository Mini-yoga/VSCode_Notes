对auto的类型推导只存在一种情况的例外（这个后面就会讨论），其他的就和模板类型推导完全一样了。
相同：
情况1：类型声明是一个指针或者是一个引用，但不是一个通用的引用
情况2：类型声明是一个通用引用
情况3：类型声明既不是一个指针也不是一个引用

```js
auto x = 27;                        // 情况3（x既不是指针也不是引用）

const auto cx = x;                  // 情况3（cx二者都不是）

const auto& rx = x;                 // 情况1（rx是一个非通用的引用）

auto&& uref1 = x;                   // x是int并且是左值
                                    // 所以uref1的类型是int&

auto&& uref2 = cx;                  // cx是int并且是左值
                                    // 所以uref2的类型是const int&

auto&& uref3 = 27;                  // 27是int并且是右值
                                    // 所以uref3的类型是int&&
                                    
//在非引用类型声明里，数组和函数名称如何退化成指针。这在auto类型推导上面也是一样     
const char name[] =                 // name的类型是const char[13] 
    "R. N. Briggs";

auto arr1 = name;                   // arr1的类型是const char*

auto& arr2 = name;                  // arr2的类型是const char (&)[13]

void someFunc(int, double);         // someFunc是一个函数，类型是
                                    // void (*)(int, double)

auto& func2 = someFunc;             // func1的类型是
                                    // void (&)(int, double)
```

不同：
对待花括号初始化的行为是auto唯一和模板类型推导不一样的地方。当auto声明变量被使用一对花括号初始化，推导的类型是std::intializer_list_的一个实例。但是如果相同的初始化递给相同的模板，类型推导会失败，代码不能编译。所以auto和模板类型推导的本质区别就是auto假设花括号初始化代表的是std::initializer_list，但是模板类型推导却不是_。

```js
auto x1 = 27;                       // 类型时int，值是27
auto x2(27);                        // 同上
auto x3 = { 27 };                   // 类型是std::intializer_list<int>
                                    // 值是{ 27 }
auto x4{ 27 };                      // 同上
auto x5 = { 1, 2, 3.0 };            // 错误！ 不能讲T推导成
                                    // std::intializer_list<T>类型推导失败是因为在花括号里面的数值并不是单一类型的。


template<typename T>
void f(std::initializer_list<T> initList);

f({ 11, 23, 9 });                   // T被推导成int，initList的
                                    // 类型是std::initializer_list<int>                                   
```

C++14允许auto表示推导的函数返回值（参看条款3），而且C++14的lambda可能会在参数声明里面使用auto。但是，这里面的使用是复用了模板的类型推导，而不是auto的类型推导。所以一个使用auto声明的返回值的函数，返回一个花括号初始化就无法编译。

```js
auto createInitList()
{
    return { 1, 2, 3 };             // 编译错误：不能推导出{ 1, 2, 3 }的类型
}
```

总结：
1 auto类型推导通常和模板类型推导类似，但是auto类型推导假定花括号初始化代表的类型是std::initializer_list，但是模板类型推导却不是这样。
2 auto在函数返回值或者lambda参数里面执行模板的类型推导，而不是通常意义的auto类型推导。
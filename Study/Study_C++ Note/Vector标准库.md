标准库Vector类型

使用需要的头文件：

\#include \<vector\>

Vector：Vector 是一个类模板。不是一种数据类型。 Vector\<int\>是一种数据类型。

一、 定义和初始化

Vector\<T\> v1; //默认构造函数v1为空

Vector\<T\> v2(v1);//v2是v1的一个副本

Vector\<T\> v3(n,i);//v3包含n个值为i的元素

Vector\<T\> v4(n); //v4含有n个值为0的元素

二、 值初始化

1\> 如果没有指定元素初始化式，标准库自行提供一个初始化值进行值初始化。

2\> 如果保存的式含有构造函数的类类型的元素，标准库使用该类型的构造函数初始化。

3\> 如果保存的式没有构造函数的类类型的元素，标准库产生一个带初始值的对象，使用这个对象进行值初始化。

三、Vector对象最重要的几种操作

1\. v.push\_back(t) 在数组的最后添加一个值为t的数据

2\. v.size() 当前使用数据的大小

3\. v.empty() 判断vector是否为空

4\. v[n] 返回v中位置为n的元素

5\. v1=v2 把v1的元素替换为v2元素的副本

6\. v1==v2 判断v1与v2是否相等

7\. ！=、\<、\<=、\>、\>= 保持这些操作符惯有含义

vector容器类型

vector容器是一个模板类，可以存放任何类型的对象（但必须是同一类对象）。vector对象可以在运行时高效地添加元素，并且vector中元素是连续存储的。

vector的构造

函数原型：

template\<typename T\>

explicit vector(); // 默认构造函数，vector对象为空

explicit vector(size\_type n, const T& v = T()); // 创建有n个元素的vector对象

vector(const vector& x);

vector(const\_iterator first, const\_iterator last);

注：vector容器内存放的所有对象都是经过初始化的。如果没有指定存储对象的初始值，那么对于内置类型将用0初始化，对于类类型将调用其默认构造函数进行初始化（如果有其它构造函数而没有默认构造函数，那么此时

必须提供元素初始值才能放入容器中）。

举例：

vector\<string\> v1; // 创建空容器，其对象类型为string类

vector\<string\> v2(10); // 创建有10个具有初始值（即空串）的string类对象的容器

vector\<string\> v3(5, "hello"); // 创建有5个值为“hello”的string类对象的容器

vector\<string\> v4(v3.begin(), v3.end()); // v4是与v3相同的容器（完全复制）

vector的操作（下面的函数都是成员函数）

bool empty() const; // 如果为容器为空，返回true；否则返回false

size\_type max\_size() const; // 返回容器能容纳的最大元素个数

size\_type size() const; // 返回容器中元素个数

size\_type capacity() const; // 容器能够存储的元素个数，有：capacity() \>= size()

void reserve(size\_type n); // 确保capacity() \>= n

void resize(size\_type n, T x = T()); // 确保返回后，有：size() == n；如果之前size()\<n，那么用元素x的值补全。

reference front(); // 返回容器中第一个元素的引用（容器必须非空）

const\_reference front() const;

reference back(); // 返回容器中最后一个元素的引用（容器必须非空）

const\_reference back() const;

reference operator[](size\_type pos); // 返回下标为pos的元素的引用（下标从0开始；如果下标不正确，则属于未定义行为。

const\_reference operator[](size\_type pos) const;

reference at(size\_type pos); // 返回下标为pos的元素的引用；如果下标不正确，则抛出异常out\_of\_range

const\_reference at(size\_type pos) const;

void push\_back(const T& x); // 向容器末尾添加一个元素

void pop\_back(); // 弹出容器中最后一个元素（容器必须非空）

// 注：下面的插入和删除操作将发生元素的移动（为了保持连续存储的性质），所以之前的迭代器可能失效

iterator insert(iterator it, const T& x = T()); // 在插入点元素之前插入元素（或者说在插入点插入元素）

void insert(iterator it, size\_type n, const T& x); // 注意迭代器可能不再有效（可能重新分配空间）

void insert(iterator it, const\_iterator first, const\_iterator last);

iterator erase(iterator it); // 删除指定元素，并返回删除元素后一个元素的位置（如果无元素，返回end()）

iterator erase(iterator first, iterator last); // 注意：删除元素后，删除点之后的元素对应的迭代器不再有效。

void clear() const; // 清空容器，相当于调用erase( begin(), end())

void assign(size\_type n, const T& x = T()); // 赋值，用指定元素序列替换容器内所有元素

void assign(const\_iterator first, const\_iterator last);

const\_iterator begin() const; // 迭代序列

iterator begin();

const\_iterator end() const;

iterator end();

const\_reverse\_iterator rbegin() const;

reverse\_iterator rbegin();

const\_reverse\_iterator rend() const;

reverse\_iterator rend();

vector对象的比较（非成员函数）

针对vector对象的比较有六个比较运算符：operator==、operator!=、operator\<、operator\<=、operator\>、operator\>=。

其中，对于operator==和operator!=，如果vector对象拥有相同的元素个数，并且对应位置的元素全部相等，则两个vector对象相等；否则不等。

对于operator\<、operator\<=、operator\>、operator\>=，采用字典排序策略比较。

注：其实只需要实现operator==和operator!=就可以了，其它可以根据这两个实现。因为，operator!= (lhs, rhs) 就是 !(lhs == rhs)，operator\<=(lhs, rhs) 就是 !(rhs \< lhs)，operator\>(lhs, rhs) 就是 (rhs \< lhs)，

operator\>=（lhs, rhs) 就是 !(lhs, rhs)。

vector类的迭代器

vector类的迭代器除了支持通用的前缀自增运算符外，还支持算术运算：it + n、it - n、it2 - it1。注意it2 - it1返回值为difference\_type（signed类型）。

注意，任何改变容器大小的操作都可能造成以前的迭代器失效。

应用示例

\#include \<iostream\>

\#include \<cassert\>

\#include \<vector\>

using namespace std;

int main()

{

vector\<string\> v(5, "hello");

vector\<string\> v2(v.begin(), v.end());

assert(v == v2);

cout\<\<"\> Before operation"\<\<endl;

for(vector\<string\>::const\_iterator it = v.begin(); it \< v.end(); ++it)

cout\<\<\*it\<\<endl;

v.insert(v.begin() + 3, 4, "hello, world");

cout\<\<"\> After insert"\<\<endl;

for(vector\<string\>::size\_type i = 0; i \< v.size(); ++i)

cout\<\<v[i]\<\<endl;

vector\<string\>::iterator it = v.erase(v.begin() + 3, v.begin() + 6);

assert(\*it == "hello, world");

cout\<\<"\> After erase"\<\<endl;

for(vector\<string\>::size\_type i = 0; i != v.size(); ++i)

cout\<\<v[i]\<\<endl;

assert(v.begin() + v.size() == v.end());

assert(v.end() - v.size() == v.begin());

assert(v.begin() - v.end() == -vector\<string\>::difference\_type(v.size()));

return 0;

}

程序说明：上面程序中用了三个循环输出容器中的元素，每个循环的遍历方式是不一样的。特别需要说明的是，第二个循环在条件判断中使用了size() 函数，而不是在循环之前先保存在变量中再使用。之所以这样做，有两个

原因：其一，如果将来在修改程序时，在循环中修改了容器元素个数，这个循环仍然能很好地工作，而如果先保存size()函数值就不正确了；其二，由于这些小函数（其实现只需要一条返回语句）基本上都被声明为inline，所

以不需要考虑效率问题。

---------------------------------

c++编程语言中有一种叫做Vector的应用方法，它的作用在实际编程中是非常重要的。在这里我们将会为大家详细介绍一下C++ Vector的相关应用技巧及基本内容，希望能给大家带来一些帮助。

(1)vector\< 类型 \> 标识符 ;

(2)vector\< 类型 \> 标识符(最大容量) ；

(3)vector\< 类型 \> 标识符（最大容量，初始所有值）；

(4) int i[4] = {12,3,4,5};

vector\< 类型 \> vi(i , i+2); //得到i索引值为3以后的值 ；

(5)vector\< vector\<int\> \> //vi 定义2维的容器；记得一定要有空格，不然会报错

vector\< int \> line

// 在使用的时候一定要首先将vi个行进行初始化;

for(int i = 0 ; i \< 10 ; i ++)

{

vector.push\_back(line);

}

/// 个人认为使用vector定义二维数组很好，

因为是长度可以不预先确定。很好。

(6)C++ Vector排序

vector\< int \> vi ;

vi.push\_back(1);

vi.push\_back(3);

vi.push\_back(0);

sort(vi.begin() , vi.end()); /// /小到大

reverse(vi.begin(),vi.end()) /// 从大道小

(7)顺序访问

vector \< int \> vi ;

for( int i = 0 ; i \< 10 ; i ++)

{

vector.push\_back(i);

}

for(int i = 0 ; i \< 10 ; i ++) /// 第一种调用方法

{

cout \<\<vector[i] \<\<" " ;

}

for(vector\<int\>::iterator it = vi.begin() ;

it !=vi.end() ; it++) ///第二种调用方法

{

cout \<\< \*it \<\< " " ;

}

(8)寻找

vector \< int \> vi ;

for( int i = 0 ; i \< 10 ; i ++)

{

vector.push\_back(i);

}

vector \< int \>::interator it = find(vi.begin() , vi.end,3) ;

cout \<\< \*it \<\< endl ; ///返回容器内找到值的位置。

(9)使用数组对C++ Vector进行初始化

int i[10] ={1,2,3,4,5,6,7,78,8} ;

///第一种

vector\<int\> vi(i+1,i+3); ///从第2个元素到第三个元素

for(vector \<int\>::interator it = vi.begin() ;

it != vi.end() ; it++)

{

cout \<\< \*it \<\<" " ;

}

(10) 结构体类型

struct temp

{

public :

string str ;

public :

int id ;

}tmp

int main()

{

vector \<temp\> t ;

temp w1 ;

w1.str = "Hellowor" ;

w1.id = 1 ;

t.push\_back(t1);

cout \<\< w1.str \<\< "," \<\<w1.id \<\<endl ;

return 0 ;

}


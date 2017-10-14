1、把string转换为char\* 有3中方法

```js

1：data   
如：   
string str = "Hello";   
char *p = str.data();   
  
2：.c_str   
如：  
string str = "Hello";   
char *p = str.c_str();   
  
3： copy   
如 :  
string str = "Hello";   
char p[40];   
str.copy(p,5,0); //这里5，代表复制几个字符，0代表复制的位置  
*(p+5)='\0';     //要手动加上结束符  
cout < <p; 
```

2、把char\* 转换为 string的方法

```js
char* p = "Hello";  
std::string str = p; // 可以对str直接赋值 
```
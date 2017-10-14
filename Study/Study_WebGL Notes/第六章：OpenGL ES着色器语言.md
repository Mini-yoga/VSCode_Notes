**1、数值类型**
  数值类型：整数和浮点
  布尔值类型：没有字符串类型

**2、GLSL ES是强类型语言**
  具体明确指出变量的类型：vec4 a_Position

**3、矢量和矩阵**
  矢量：表示坐标或颜色值（vec2/3/4,ivec2/3/4,bvec2/3/4）<其中数字表示多少个分量>
  矩阵：表示变换矩阵（mat2/3/4）<其中数字表示多少阶矩阵>
  
**  4、矩阵构造函数**
  要保证存储在矩阵中的元素是按照列主序排列的
  <font color="#4590a3" size="2px">mat 4 m4 = mat4(1.0)相当于四阶斜对角线上元素都是1.0</font>
  
**  5、访问元素**
  获取顶点坐标分量：x,y,z,w
  获取颜色分量：r,g,b,a
  获取纹理坐标分量：s,t,p,q
  <font color="#4590a3" size="2px">混合抽取分量：v2 = v3.xy抽取x,y两个分量合成一个v2,可逆序，可跳过任意分量</font>
  
**  6、[]运算符**
  在[]中只能出现的索引值必须是常量索引值，定义如下：
  1）整型字面量
  2）用const修饰的全局变量或者局部变量
  3）循环索引
  4）由前三条中的项组成的表达式
  
**  7、结构体**
  使用关键字struct，将已存在的类型聚合在一起，就可以定义为结构体

```js
struct light{
  vec4 color;
  vec3 position;
}l1
```

```js
l1 = light(vec4(0.0,1.0,0.0,1.0),vec3(8.0,3.0,0.0))
```

**8、数组**
只支持一维数组，不支持pop和push，不需要new运算符

```js
float fArray[4]//声明
```

1）要求整型字面量
2）const限定字修饰的全局变量或局部变量（也就是说const int size = 4 vec4 vArray = [size],无const会出错）

**9、取样器**
1）通过该类型变量访问纹理，有两种基本类型：sampler2D和samplerCube。
2）取样器变量只能是uniform变量，或者需要访问纹理的函数。
3）唯一能赋值给你取样器变量的是纹理单元编号。
4）必须用gl.uniformli()进行赋值。

10、continue、break、discard(只能在片元着色器中使用）

**11、规范声明：**
如果函数定义在调用后，则在调用前先声明函数的规范

```js
float luma(vec4);
float bri = luma(vec4);
float luma(vec4 color){
  return 1;
}
```

**10、参数限定词**
in:默认，参数传入函数，可使用修改但不会影响传入的变量
const in：参数传入函数，可使用但不能修改
out：传入变量的引用，在函数内被修改，可被修改，无返回值也可改变外部传入的变量
inout：传入变量的引用，会用变量初始值，修改变量的值，影响到外部传入的变量

**11、存储限定字**
1）const：变量的值不能被改变
2）Attribute：只能出现在顶点着色器中，是全局变量，表示**逐顶点**信息。变量类型只能是float,vec2/3/4,mat2/3/4。Attribute变量的最大数目与设备有关，至少八个。
3）uniform：可以出现在顶点着色器和片元着色器中，是全局变量，是只读变量，可以是除数组和结构体外任意类型，如果顶点着色器和片元着色器中声明同名变量，可以被着色器共享，uniform变量的最大数目与设备有关，至少128个。
4）varing：任务是从顶点着色器向片元着色器传输数据，其中发生光栅化过程，根据绘制图形，对顶点着色器变量进行内插再传递给片元着色器，是全局变量，必须同名同类型，变量类型只能是float,vec2/3/4,mat2/3/4。varing变量的最大数目与设备有关，至少八个。

**12：精度限定字：**
目的是为了提高着色器运行效率，减少内存开支。高精度和低精度的选择能控制程序效果和性能之间的平衡。

```js
precision highp float;//precision指默认所有float都是hiphp
mediump float size;
lowp vec4 color;
gl.getShaderPrecisionFormat()//来检查数值范围和精度
```

只有片元着色器在的float没有默认精度，需要指定，否则会报错。

13、预处理指令
用来真正编译之前对代码进行预处理，以#开始

```js
#ifdef GL_ES
precision highp float;//检查了是否已经定义了GL_ES宏，如果是则执行
#endif

#ifndef GL_ES
precision highp float;//检查了是否已经定义了GL_ES宏，如果否则执行
#endif

#define 宏名 宏内容//定义宏
#undef 宏名//解除宏定义


#define NUM 100
#if NUM==100
.....
#else
....
#endif


#version 101//指定着色器使用的GLSL ES版本
```














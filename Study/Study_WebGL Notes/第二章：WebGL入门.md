1、在canvas标签元素上绘制步骤：
 <font color="#4590a3" size="2px">
 a:获取canvas元素id
 b:向该元素请求二维图形的“绘图上下文”
 x=canvas,getCotext('2d')
 c:在绘图上下文上调用相应的绘图函数，绘制图形
 x.fillStyle(R,G,B,A) x.fillRect(x,y,width,hight)</font>
 

2、着色器：以字符串的形式嵌入在JS文件中
顶点着色器：用来描述顶点特性的程序（位置，颜色）。
片元着色器：进行逐片元处理过程的程序（光照），可以理解为像素。

<font color="#225a1f" size="2px">片元着色器有一项必须完成的任务：设置或消除变量 gl_FragColor，另一个四维浮点变量，也就是片元点最终的颜色。什么是片元？想象一个具有三个顶点的三角形，片元就是经过这三个顶点计算后的，所有在三角形内部的点。因此，片元值由顶点的值内插生成。如果一个顶点的颜色是红色，相邻顶点的颜色是蓝色，那么我们可以观测到颜色从红色顶点附近渐变，由红色变成紫色，最终在蓝色顶点附近变成蓝色。</font>

 <font color="#4590a3" size="2px">执行流程：浏览器执行加载JS程序----执行WebGL相关方法(着色器)----渲染到颜色缓冲区----显示
着色器语言以JS字符串形式编写（用+号连接多个字符串，每行以\n结束，不是必须，但是可以用于定位错误行数）这样就能传入到WebGL系统了。</font>
 

3、WebGL程序流程(main函数)：
<font color="#CD5C5C" size = "2px">获取canvas元素——获取WebGL上下文——**初始化着色器**——设置canvas背景色——清除canvas——**绘图**</font>
<font color="#4590a3" size="2px">WebGL程序包括运行在浏览器的JS和运行在WebGL系统的着色器两部分。</font>

WebGL系统由顶点着色器和片元着色器组成，我们要将字符串形式的着色器代码从JS传入WebGL，着色器运行在WebGL中不是JS程序中。

4、顶点着色器

```js
var VSHADER_SOURCE = 
‘void main(){\n'+
'gl_Position = vec4(0.0,0.0,0.0,1.0);\n'+
'gl_PointSize = 10.0;\n'+
'}\n’; 
```

注意：
1）gl_Position为必须赋值变量
2）vec4表示四个浮点数组成的矢量
3）4个分量组成的矢量成为<font color="#4590a3" size="2px">齐次坐标</font>，如果最后一个分量为1.0则可以表示前三个分量坐标的那个点，（x,y,z,w）的意思为（x/w,y/w,z/w）因此可以表示无穷远。

5、片元着色器

```js
var FSHADER_SOURCE = 
‘void main(){\n'+
'gl_FragColor = vec4(1.0,0.0,0.0,1.0)'+
'}\n’; 
```

注意：gl_FragColor是唯一一个内置变量

6、绘图操作
<font color="#CD5C5C" size = "2px">gl.drawArrays(mode,first,count)方法用于绘制图形</font>

7、WebGL坐标系统与canvas绘图区坐标系
WebGL坐标系统遵守右手坐标系原则，上y 右x 外z
canvas绘图区坐标系，左为上边缘，右为下边缘，上为右边缘，下为左边缘



8、JS和着色器之间传输数据
为了将位置信息从JS程序中传给顶点着色器，而不是直接着色器内直接编写，引入attribute（顶点相关数据）和uniform（顶点无关数据）变量。

使用attribute变量步骤：
1）在顶点着色器中声明attribute变量（attribute 类型 变量名 放在字符串前）
2）将attribute变量赋值给gl_Position变量(原来字符串中的数据由变量名代替)
3）向attribute变量传输数据(在JS内使用initShader()初始化着色器；使用gl.getAttribLocation(gl.program,‘变量名’)来获取attribute变量的地址,无则返回-1；使用gl.vertexAttrib3f(上一步存储的变量名,x,y,z))

<font color="#4590a3">同族函数：gl.vertexAttrib3f同族函数为gl.vertexAttrib1f、gl.vertexAttrib2f、gl.vertexAttrib4f数字代表参数个数</font>

使用uniform变量步骤：
1）在片元着色器中设置精度
2）在片元着色器中声明uniform变量
3）将uniform变量赋值给gl_FragColor变量
4) 向uniform变量传输数据(在JS内使用initShader()初始化着色器；使用gl.getUniformLocation(gl.program,‘变量名’)来获取变量的地址，无则返回null；使用gl.uniform4f(上一步存储的变量名,x,y,z))

<font color="#4590a3">顶点着色器能使用attribute、uniform变量，是片元着色器不能使用attribute变量但能使用uniform和varing变量

<font color="#225a1f">1.Uniforms变量既可以传入顶点着色器，也可以传入片元着色器，它们包含了哪些在整个渲染过程中保持不变的变量，比如，一个点光源的位置。
2.Attributes变量对应于每个顶点，它们只可以传入顶点着色器中，比如每个顶点都具有一个颜色。Attributes变量和顶点的关系是一一对应的。
3.Varyings变量是在顶点着色器中定义，并且准备传入给片元着色器的变量。为了确保这点，我们需要确保在两个着色器中变量的类型和命名完全一致。一个经典的应用是法线向量，因为在计算光照的时候需要用到法线。</font>





**Demo总结：**
1）varying mediump vec4 XXX 定义在顶点着色器与片着色器中，首先当顶点着色器运行绘制点后，将会把数据传入片着色器，进行对顶点间区域像素的渲染，varing变量是顶点着色器与片着色器之间传输数据的渠道，流水线为JS-顶点着色器-片元着色器。使用uniform变量，直接在片元着色器中设置,也能达到目的）

2）匿名函数：canvas.onmousedown = function(){click();} 注册事件响应函数，可以访问局部变量

3）交互时，需要把浏览器客户区坐标系转换到canvas坐标系。

```js
		var rect = ev.target.getBoundingClientRect();//canvas的原点在浏览器客户端的坐标
		x = ((x-rect.left) - canvas.height/2)/(canvas.height/2);
		y = (canvas.width/2 - (y-rect.top))/(canvas.width/2);
```






















1）设置后期处理：引入各种通道库（实现比如添加胶片效果等）
（1）创建一个EffectComposer效果组合对象（多种通道效果叠加对象），在上面添加后期处理通道。
（2）配置该对象，使它可以渲染我们的场景，并应用额外的后期处理步骤。
（3）在rander循环中，使用EffectComposer渲染场景、应用通道。并输出结果。
<font color="#4590a3" size="2px">注意：每个通道都会按照其加入EffectComposer的顺序执行，不是所有通道都可以将结果输出到屏幕的通道，FilmPass通道可以。然后添加的组合器中，用效果组合代替WebGLRenderer</font>

2）后期处理通道：
（1）Three.js提供了几个后期处理通道，可以直接添加到EffectComposer对象中：
BloomPass:使明亮区域渗入较暗的区域，模拟相机找到过多亮光的情形，有泛光效果。(<font color="#4590a3" size="2px">属性:Strength,kernelSize,sigma,Resolution</font>)
DotScreenPass:将一层黑点贴到代表原始图的屏幕上。(<font color="#4590a3" size="2px">属性:center,angle,scale</font>)
FilePass:通过扫描线和失真模拟电视屏幕。（<font color="#4590a3" size="2px">属性:nosieIntensity,scanlinesIntensity,scanlinesCount,grayscale</font>）
MaskPass:在当前图片贴上一层掩膜，后续通道只会影响被贴的区域。
RenderPass:在指定场景和相机的基础上渲染出一个新的场景。
SavePass:会将渲染步骤的结果复制一份，方便以后使用，作用不大。
ShaderPass:传入一个自定义着色器，用来生成高级的自定义的后期处理通道。
TexturePass:将效果器的当前状态保存为一个纹理，然后可以在其他EffectCompose对象中将该纹理作为输入参数。
<font color="#4590a3" size="2px">多个渲染器输出结果显示在同一屏幕上：WebGLRenderer.autoClear属性设为false，并且将视图区分成不同的部分。</font>

（2）使用掩膜的个高级效果组合器：在特定区域上应用通道
a.创建一个作为背景图的场景
b.创建一个场景，地球
c.创建一个场景，火星 
d.创建一个EffectComposer对象，三个场景渲染到一个图片里
e.渲染成火星的球体应用一个彩色效果
f.渲染成地球的球体应用一个褐色效果
<font color="#4590a3" size="2px">注意：同一光源不能配置到不同场景中，每个场景创建各自的光源</font>

（3）用ShaderPass定制效果：可以传递自定义着色器，将大量额外的效果应用到场景中。
a.简单着色器：
MirrorShader:为部分屏幕创建镜面效果。
HueStaturationShader:改变颜色的色调和饱和度。
VignetteShader:添加晕映效果，可以在图片中央的周围显示黑色的边框。
ColorCorrectionShader:调整颜色的分布。
RGBShiftShader:将构成颜色的红绿蓝分开。
BrightnessContrasShader:更改图片的亮度和对比度。
ColorifyShader:在屏幕上蒙上一层颜色。
SepiaShader:创建类似乌贼墨的效果。

b.模糊效果着色器：
HorizontalBlurShader和VerticalBlurShader:在整个场景中应用模糊效果。
HorizontalTiltShiftShader和VerticalTiltShiftShader:创建出移轴效果，只有部门图片显示比较锐利，看上去像微缩景观。
TriangleBlurShader:基于三角形的方法，在场景中应用模糊效果。

c.高级效果的着色器：
BleachBypassShader:创建一种漂白效果，图片像镀银。
EdgeShader:探测图片中锐利的边界，突出显示这些边界。
FXAAShader:应用抗锯齿效果
FocusShader:中央区域渲染的锐利，周围比较模糊。

3）定制灰度图着色器：
创建自定义着色器需要实现两个组件：vertexShader(调整每个顶点位置)和fragmentShader(决定每个像素的颜色)
<font color="#4590a3" size="2px">注意：编写着色器代码需要用OpenGL着色器语言，可参考<http://www.khronos.org/opengles/sdk/docs/manglsl></font>

4）定制位着色器：
通过这个着色器可以自动将24位输出转换成颜色深度任意的结果。

**总结：**
（1）如果想重用某个EffectComposer的结果，可以使用TexurePass。
（2）如果EffectComposer中有多个RenderPass，那么将clear属性设置为false，不然只会看到最后一个Renderer输出。
（3）如果只想在特定对象上应用某种效果，可以使用MaskPass，用完后调用ClearMaskPass来清除掩膜。
（4）在Three.js库中使用标准方法为创建自定义着色器只需要创建fragmentShader。
















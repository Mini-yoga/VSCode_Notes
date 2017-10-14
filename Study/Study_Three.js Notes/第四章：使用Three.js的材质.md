###（1）材质（光，颜色，几何形状）：
####基础材质
**MeshBasicMaterial(网络基础材质)**：赋予简单颜色或几何体线框
<font color="#4590a3" size ="2px">属性：color,wireframe,wireframeLinewidth,wireframeLinecap(端点如何显示，平，圆，方),wireframeLinejoin(线段连接点如何显示，圆，尖角，斜角),shading,vertexColor,fog</font>

**MeshDepthMaterial(网络深度材质)**：根据到相机的距离，决定如何染色，与其他材质结合创建逐渐消失的效果
<font color="#4590a3" size = "2px">属性：wireframe,wireframeLinewidth</font>

**MeshNormalMaterial(网格法向材质)**：根据法向向量计算颜色，法向量所指的方向决定每个面从材质获得的颜色，使用THREE.ArrowHelper添加法向量
<font color="#4590a3" size = "2px">属性：wireframe,wireframeLinewidth,shading(THREE.FlatShading，THREE.SmoothShading)</font>

**MeshFaceMaterial(网络面材质)**：是一个容器，可为各表面指定不同颜色，使用数组的形式，设置各个面加入。

####高级材质
**MeshLambertMaterial(网格朗伯材质，不光亮)**：考虑光照的影响，创造颜色暗淡的不光亮的物体
<font color="#4590a3" size = "2px">属性：color,fog,vertxColor,wireframe,wireframeLinewidth,wireframeLinecap,wireframeLinejoin,shading(THREE.FlatShading，THREE.SmoothShading)</font>
<font color="#CD5C5C" size = "2px">特殊属性：ambient(环境色，与环境光一起使用，颜色相乘，默认为白色),emissive(材质发射的颜色，不受其他光照影响的颜色，默认黑色)</font>

**MeshPhongMaterial(网格Phong式材质，光亮)**：考虑光照影响，创建光亮物体
<font color="#4590a3" size = "2px">属性：color,fog,vertxColor,wireframe,wireframeLinewidth,wireframeLinecap,wireframeLinejoin,shading(THREE.FlatShading，THREE.SmoothShading)</font>
<font color="#CD5C5C" size = "2px">特殊属性：ambient(环境色，与环境光一起使用，颜色相乘，默认为白色),emissive(材质发射的颜色，不受其他光照影响的颜色，默认黑色).specular(指定材质光亮程度以及高光部分，若设置成与color相同颜色产生金属品质),shininess(指定高光亮度)</font>

**ShaderMaterial(着色器材质)**：允许自定义着色器，直接控制定点的放置方式与像素的着色方式,着色器可以将JavaScript对象转换为屏幕上的像素
<font color="#4590a3" size = "2px">属性：fog,vertxColor,wireframe,wireframeLinewidth,shading(THREE.FlatShading，THREE.SmoothShading)</font>
<font color="#CD5C5C" size = "2px">特殊属性：fragmentShader(像素着色器，定义每个像素的颜色),vertexShader(定点着色器，修改顶点位置来变换几何体),uniform(统一值，向着色器，顶点，片段发送信息),defines(设置全局变量),attributes(修改顶点与片段，用于传递数据，为所有顶点提供信息),lights(光照数据是否传给着色器，默认false)</font>

**LineBasicMaterial(直线基础材质)**：用于THREE.Line直线几何体着色
<font color="#4590a3" size = "2px">属性：fog,color,vertxColor,linewidth,LineCap,LineJoin</font>

**LineDashedMaterial(虚线材质)**：同上虚线效果
<font color="#4590a3" size = "2px">属性：fog,color,vertxColor,linewidth,LineCap,LineJoin</font>
<font color="#CD5C5C" size = "2px">特殊属性：scale(缩放dashSize,gapSize比例),dashSize,gapSize</font>



###（2）共有属性：

1）**基础属性**:ID,name,opacity,transparent（是否透明）,overdraw（画布渲染器有缝隙时）,visible,side（材质对应面）,needsUpdate

2）**融合属性**：
blending(融合，标准融合模式，以下三个属性的合成是自定义融合)：物体上的材质与背景融合，只显示上层材质
blendsrc(融合源)：如何跟背景融合，默认SrcAlphaFactor,使用alpha通道融合
blenddst(融合目标)：定义融合时如何使用背景，默认OneMinusSrcAlphaFactor,使用源alpha通道融合
blendingequation(融合公式)：如何使用以上两个属性的值，默认AddEquation,颜色值相加

3）**高级属性**：depthTest,depthWrite,alphaTest

##总结：

1）场景中所有物体都使用该材质

```js
scene.overrideMaterial = new THREE.MeshDepthMaterial();
```

2）MeshDepthMaterial没有设置颜色的属性，需要用颜色材质与物体材质融合的方法染色

3）一个组的方块绕轴旋转，其中各个小方块的位置设定为相对组的偏移

```js
group.rotation.y = step += controls.rotationSpeed;
```

4）MeshFaceMaterial在建立魔方显示魔方中的划分线时，可以使用减少0.1的边长来实现

5）直线与虚线材质唯一的区别是虚线必须调用方法computeLineDistance()来显示虚线
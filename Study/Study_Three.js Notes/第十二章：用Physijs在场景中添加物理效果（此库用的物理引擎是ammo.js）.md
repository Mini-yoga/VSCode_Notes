1）创建可用Physijs的基本Three.js场景
Physijs库：<http://chandlerprall.github.io/Physijs/>
ammo.js库：<http://github.com/kripken/ammo.js>
Web workers规范定义:<http://www.whatwg.org/specs/web-apps/current-work/multipage/workders.html>
<font color="#4590a3" size="2px">注意：Physijs不在render线程中工作而是在后台线程中执行计算，由Web workers规范定义</font>

2）材质属性：
restitution（弹性）:定义一个对象在碰撞后复原时所具有的能量
friction:对重力的敏感度

3）基础图形：Physijs提供了一些可以用来包装几何体的图形类，唯一要做的是将THREE.Mesh的构造函数替换成以下网格对象的构造函数。
Physijs.PlaneMesh:创建厚度为0的平面
Physijs.BoxMesh:方块网格
Physijs.SphereMesh:球网格
Physijs.CylinderMesh:柱状网格
Physijs.ConeMesh:圆锥网格
Physijs.CapsuleMesh:胶囊网格
Physijs.ConvexMesh:凸包网格
Physijs.ConcaveMesh:对复杂图形进行细致的表现
Physijs.HeightfieldMesh:创建出一个高度场，很容易创建出一个凸起和洼地的形状，对不同的高度做出正确的反应

4）使用约束限制对象移动：
PointConstraint:可以将一个对象与另一个对象之间的位置固定下来，如果一个动，另一个也动
HingeConstraint:限制一个对象只能像活页一样移动，例如门
SliderConstraint:对象移动在一个轴上，比如移门
ConeTwistConstraint:用一个对象限制另一个对象的旋转和移动，类似胳膊在肩关节活动
DOFConstraint:通过自由度约束，可以限制对象在任意轴上的活动，可设置对象活动的最小最大角度






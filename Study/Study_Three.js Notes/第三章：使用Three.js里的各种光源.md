1）光源

基础光：AmbientLight: 环境光（添加到当前环境中所有对象的颜色上，可以调节颜色，不能制造阴影，搭配其他光源使用）

 PointLight: 点光源（一点，不会产生阴影，会加重GPU负担）

 SpotLight: 聚光灯光源（手电筒，产生阴影，越远越暗淡）

 DirectionalLight: 方向光（平行光，太阳，光强与距离无关）

特殊光： HemisphereLight: 半球光（自然的室外光）

 AreaLight: 面光源（散发光的平面）

 LensFlare: 镜头眩光（不是光源但能添加眩光效果）
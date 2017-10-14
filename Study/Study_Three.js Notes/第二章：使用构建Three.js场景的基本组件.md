##总结：

1）设置雾效果与一句代码设置材质与颜色

```js
scene.fog = new THREE.FogExp2(0xffffff,0.01)
scene.overrideMaterial = new THREE.MeshLambertMaterial({color:0xffffff})//所有添加到场景的物体同样的材质和颜色，这种材质可以创建一些不发光的物体，且可以对添加的光源做出一些反应

```

2）几何图形可以自定义，材质可用数组定义多材质

```js
 var mesh = THREE.SceneUtils.createMultiMaterialObject(geom,materials); //多种材质创建网格函数，为每个指定材质创建实例，组中有许多子对象（gemo是一个有多个面的数组）       
```

3）translateX，translateY，translateZ可以平移几何体

4）PerspectiveCamera（视场，长宽比，近面，远面） 透视照相机（更接近真实世界）

OrthographicCamera（左边界，右边界，上边界，下边界，近面，远面）正投影照相机（渲染尺寸都一样,达到模拟城市效果）

5）设置几何体位置：cube.position.set(x,y,z)

6）随着照相机移动：camera.lookAt(new THREE.Vector3(x,y,z))
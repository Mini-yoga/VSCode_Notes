openGL ES是openGL的一个子集（但也有交叉不相等的地方），openGL是一系列的3d图形API，而openGL ES是实现在一些嵌入式系统主要是手机（安卓、苹果）和浏览器的openGL标准。

WebGL 是基于 OpenGL ES 2.0 的 Javascript API。通过 HTML5 的 Canvas 来和 DOM 打交道。因此也和 OpenGL ES 2.0 一样，使用 GLSL 作为 Shading Language （一种 C-Like 顶点计算和着色的语言，缓存编译到 GPU，由 GPU 来执行）。Three.js则封装了webGL的底层接口。

http://web.eecs.umich.edu/~sugih/courses/eecs487/common/notes/APITables-zhs.xml
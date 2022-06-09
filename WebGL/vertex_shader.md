# 顶点着色器
描述顶点特性（如位置、颜色等）的程序。顶点是二维或三维空间中的一个点。

## 内置常量
- gl_MaxVertexTextureImageUnits >0 0
- gl_MaxVertexAttribs >= 8
- gl_MaxVertexUniformAttribs >= 128
- gl_MaxVaryingVectros >= 8

## 内置变量
- gl_Position：表示顶点的位置
- gl_PointSize：表示顶点的尺寸（像素数），默认1.0；只有在gl.POINTS绘制模式时有效

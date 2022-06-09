# 片元着色器
进行逐片元处理过程（如光照等）的程序。片元是一个WebGL的属于，相当于像素。

## 内置常量
- gl_MaxTextureImageUnits >= 8
- gl_MaxFragmentUniformVectors >= 16

## 内置变量
- gl_FragColor：指定片元颜色（RGBA格式）
- gl_FragCoord：该内置变量的第一个和第二个分量表示片元在canvas坐标系统中的坐标值

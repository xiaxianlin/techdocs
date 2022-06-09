# GLSL语法
## 基本语法
- 参数限定字
	- in
	- const in
	- out 
	- const out
- 存储限定字
	- const：限定数据不可变
	- attribute：传输与顶点有关的数据
	- uniform：传输与顶点无关或者顶点共有的数据
	- varying：将顶点着色器中的值传递给片元着色器中相同类型、名称的变量，顶点着色器中的变量在传入片元着色器之前的经过了内插过程
- 精度限定字
	- precision：精度声明
	- highp：高精度，顶点着色器的最低精度。float(-2^62 ~ 2^62 | 2^-16)，int(-2^16 ~ 2^16)
	- mediump：中精度，介于高与低之间，片元着色器的最低精度。float(-2^14 ~ 2^14 | 2^-10)，int(-2^10 ~ 2^10)
	- lowp：低精度，可以表示所有颜色。float(-2 ~ 2 | 2^-8)，int(-2^8 ~ 2^8)
- 预处理指令
	- 内置宏
		- GL_ES
		- GL_FRAGMENT_PERCISION_HIGH
```glsl
// 定义宏
#define <name> <content>
#undef <name>
// 条件判断
#if <表达式>
#endif
// 判断是否定义宏
#ifdef <宏>
#endif
// 判断是否未定义宏
#ifndef <宏>
#endif
// 指定着色器版本
#version
```

## 数据类型
- 基本类型
	- float：浮点数
	- int
	- bool
- 矢量类型
	- vec2：二维矢量
	- vec3：三维矢量
	- vec4：四维矢量
	- ivec2
	- ivec3
	- ivec4
	- bvec2
	- bvec3
	- bvec4
	- 分量名
		- x,y,z,w：用来获取顶点坐标分量
		- r,g,b,a：用来获取颜色分量
		- s,t,p,q：用来获取纹理坐标分量
- 矩阵类型
	- mat2：二维方阵
	- mat3：三维方阵
	- mat4：四维方阵
- 结构体
	- struct <name> {}
- 数组
- sampler2D：绑定到gl.TEXTURE_2D上的纹理数据类型
- samplerCube：绑定到gl.TEXTURE_CUEB_MAP上的纹理数据类型 

## 内置函数
- 类型转换
  - int()
  - float()
  - bool()
- 矢量构造函数
  - vec2()
  - vec3()
  - vec4()
- 矩阵构造函数
  - mat2()
  - mat3()
  - mat4()
- 角度函数
  - radians()：角度制转弧度制
  - degrees()：弧度制转角度制
- 三角函数
  - sin()：正弦
  - cos()：余弦
  - tan()：正切
  - asin()：反正弦
  - acos()：反余弦
  - atan()：反正切
- 指数函数
  - pow()：x^y
  - exp()：自然指数
  - log()：自然对数
  - exp2()：2的x次方
  - log2()：以2为底对数
  - sqrt()：开平方
  - inversesqrt()：开平方的倒数
- 通用函数
  - abs()：绝对值
  - min()：最小值
  - max()：最大值
  - mod()：取余数
  - sign()：取正负号
  - floor()：向下取整
  - ceil()：向上取整
  - clamp()：限定范围
  - mix()：线性内插
  - step()：步进函数
  - smoothstep()：艾米内插步进
  - fract()：获取小数部分
- 几何函数
  - length()：矢量长度
  - distance()：两点间距离
  - dot()：內积
  - cross()：外积
  - normalize()：归一化
  - reflect()：矢量反射
  - faceforward()：使向量”朝前“
- 矩阵函数
  - matrixCmpMult()：逐元素乘法
- 矢量函数
  - lessThan()：逐元素小于
  - lessThanEqual()：逐元素小于等于
  - greaterThan()：逐元素大于
  - greaterThanEqual()：逐元素大于等于
  - equal()：逐元素相等
  - notEqual()：逐元素不等
  - any()：任一元素为true则为true
  - all()：所有元素为true则为true
  - not()：逐元素取补
- 纹理查询函数
  - texture2D(sampler2D sampler, vec2 coord)：从sampler指定的纹理上获取coord指定的纹理坐标的像素颜色
  	- sampler：指定的纹理单元编号
  	- coord：指定的纹理坐标
  	- return：纹理坐标处的像素的颜色值，其格式由gl.textImage2D()的internalformat参数决定
  - textureCube()：在立方体纹理中获取纹素
  - texture2DProj()：texture2D的投影版本
  - texture2DLod()：texture2D的金字塔版本
  - textureCubeLod()：textureCube的金字塔版本
  - texture2DPorjLod()：texture2DLod的投影版本

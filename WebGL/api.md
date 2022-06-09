# 基础API
## 全局常量
- gl.VERTEX_SHADER：顶点着色器
- gl.FRAGMENT_SHADER：片元着色器
- gl.COLOR_BUFFER_BIT：指定颜色缓存
- gl.DEPTH_BUFFER_BIT：指定深度缓冲区
- gl.STENCIL_BUFFER_BIT：指定模板缓冲区
- cap功能
	- gl.DEPTH_TEST：隐藏面消除
	- gl.BLEND：混合
	- gl.POLYGON_OFFSET_FILL：多边形位移
- 着色器参数
	- gl.COMPILE_STATUS：编译状态
	- gl.SHADER_TYPE：着色器类型
	- gl.DELETE_STATUS：删除状态
- 程序参数
	- gl.LINK_STATUS：链接状态
	- gl.DELETE_STATUS：删除状态
	- gl.VALIDATE_STATUS：验证状态
	- gl.ATTACHED_SHADERS：已被分配给程序的着色器数量
	- gl.ACTIVE_ATTRIBUTES：顶点着色器中的attribute变量的数量
	- gl.ACTIVE_UNIFORMS：程序中uniform变量的数量
- 绘制模式
	- gl.POINTS：点，一系列点，绘制在v0、v1、v2…处
	- gl.LINES：线段，一系列单独的线段，绘制在(v0, v1)、(v2, v3)、(v4, v5)…
	- gl.LINE_STRIP：线条，一系列连接的线条，绘制在(v0, v1)、(v1, v2)、(v2, v3)…
	- gl.LINE_LOOP：回路，一系列连接的线条，绘制在(v0, v1)、(v1, v2)…(vn, v0)
	- gl.TRIANGLES：三角形，一系列单独的三角形，绘制在(v0, v1, v2)、(v3, v4, v5)…
	- gl.TRIANGLE_STRIP：三角带，一系列带状的三角形，绘制在(v0, v1, v2)、(v2, v1, v3)、(v2, v3, v4)…
	- gl.TRIANGLE_FAN：三角扇，一系列三角形组成的类似于扇形的图形，绘制在(v0, v1, v2)、(v0, v2, v3)、(v0, v3, v4)…
- 缓冲区
	- gl.FRAMEBUFFER
	- gl.RENDERBUFFER
	- 绘制参数
		- gl.ARRAY_BUFFER：表示缓冲区对象中包含了顶点数据
		- gl.ELEMENT_ARRAY_BUFFER：表示缓缓冲区对象中包含了顶点的索引值
	- 数据使用方式
		- gl.STATIC_DRAW：只会向缓冲区对象中写入一次数据，但需要绘制很多次
		- gl.STREAM_DRAW：只会向缓冲区对象中写入一次数据，然后绘制若干次
		- gl.DYNAMIC_DRAW：会向缓冲区对象中多次写入数据，并绘制很多次
	- 渲染缓冲区
		- gl.DEPTH_COMPONENT16：表示渲染缓冲区将代替深度缓冲区
		- gl.STENCIL_INDEX8：表示渲染缓冲区将代替模板缓冲区
		- gl.RGBA4：表示渲染缓冲区将代替颜色缓冲区，4个分量各占4个比特
		- gl.RGB5_A1：表示渲染缓冲区将代替颜色缓冲区，RGB分量各占5个比特，A占据1个比特
		- gl.RGB565：表示渲染缓冲区将代替颜色缓冲区，RGB分量各分别占据5、6、5个比特
	- 关联类型
		- gl.COLOR_ATTACHMENT0：表示颜色关联对象
		- gl.DEPTH_ATTACHMENT：表示深度关联对象
		- gl.STENCIL_ATTACHMENT：表示模板关联对象
	- 帧缓冲区检查状态
		- gl.FRAMEBUFFER_COMPLETE：是否正确配置
		- gl.FRAMEBUFFER_INCOMPLETE_ATTACHMENT：关联对象不合法
		- gl.FRAMEBUFFER_INCOMPLETE_DIMENSIONS：关联对象尺寸不一致
		- gl.FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT：未检查到关联对象
- 数据格式
	- gl.UNSIGNED_BYTE
	- gl.SHORT
	- gl.UNSIGNED_SHORT
	- gl.INT
	- gl.UNSIGNED_INIT
	- gl.FLOAT
- 纹理
	- gl.UNPACK_FLIP_Y_WEBGL：对图像进行Y轴反转。默认值为false
	- gl.UNPACK_PREMULTIPLY_ALPHA_WEBGL：将图像RGBA颜色值的每一个分量乘以A。默认值为false
	- gl.TEXTURE0：0号纹理单元
	- gl.TEXTURE1：1号纹理单元
	- gl.TEXTURE2：2号纹理单元
	- gl.TEXTURE3：3号纹理单元
	- gl.TEXTURE4：4号纹理单元
	- gl.TEXTURE5：5号纹理单元
	- gl.TEXTURE6：6号纹理单元
	- gl.TEXTURE7：7号纹理单元
	- gl.TEXTURE_2D：二维纹理
	- gl.TEXTURE_CUBE_MAP：立方体纹理
	- 纹理参数
		- gl.TEXTURE_MAG_FILTER：纹理放大
		- gl.TEXTURE_MIN_FILTER：纹理缩小
		- gl.TEXTURE_WRAP_S：纹理水平填充
		- gl.TEXTURE_WRAP_T：纹理锤子填充
	- 纹理参数值
		- gl.NEAREST：使用原纹理上距离映射后像素中心最近的那个像素的颜色值，作为新像素的值（使用曼哈顿距离），用于纹理缩放
		- gl.LINEAR：使用距离新像素中心最近的四个像素的颜色值的加权平均，作为新像素的值。（比NEAREST效果好，但是开销大），用于纹理缩放
		- gl.NEAREST_MINMAP_NEAREST
		- gl.LINEAR_MINMAP_NEAREST
		- gl.NEAREST_MINMAP_LINEAR
		- gl.LINEAR_MINMAP_LINEAR
		- gl.REPEAT：平铺式的重复纹理，用于纹理填充
		- gl.MIRRORED_REPEAT：镜像对称式的重复纹理，用于纹理填充
		- gl.CLAMP_TO_EDGE：使用纹理图像边缘值，用于纹理填充
	- 纹理数据格式
		- gl.RGB：红、绿、蓝
		- gl.RGBA：红、绿、蓝、透明度
		- gl.ALPHA：(0.0, 0.0, 0.0, 透明度)
		- gl.LUMINANCE：L、L、L、1L：流明
		- gl.LUMINANCE_ALPHA：L、L、L，透明度
	- 纹理数据的数据格式
		- gl.UNSIGNED_BYTE：无符号整型，每个颜色分量占据1字节
		- gl.UNSIGNED_SHORT_5_6_5：RGB，每个分量分别占据5、6、5比特
		- gl.UNSIGNED_SHORT_4_4_4_4：RGBA，每个分量分别占据4、4、4、4比特
		- gl.UNSIGNED_SHORT_5_5_5_1：RGBA，每个分量分别占据5比特，A分量占据1比特
- 混合权重因子
	- gl.ZERO：(0.0, 0.0, 0.0)
	- gl.ONE：(1.0, 1.0, 1.0)
	- gl.SRC_COLOR：(Rs, Gs, Bs)
	- gl.ONE_MINUS_SRC_COLOR：(1 - Rs, 1 - Gs, 1 - Bs)
	- gl.DST_COLOR：(Rd, Gd, Bd)
	- gl.ONE_MINUS_DST_COLOR：(1 - Rd, 1 - Gd, 1 - Bd)
	- gl.SRC_ALPHA：(As, As, As)
	- gl.ONE_MINUS_SRC_ALPHA：(1 - As, 1 - As, 1 - As)
	- gl.DST_ALPHA：(Ad, Ad, Ad)
	- gl.ONE_MINUS_DST_ALPHA：(1 - Ad, 1 - Ad, 1 - Ad)
	- gl.SRC_ALPHA_SATURATE：(min(As, Ad), min(As, Ad), min(As, Ad))

## 全局API
#### gl.enable(cap)：开启cap表示的功能
- cap：指定需要开启的功能，参考全局常量cap功能

#### gl.disable(cap)：关闭cap表示的功能
- cap：指定需要开启的功能，参考全局常量cap功能

#### gl.clear(buffer)：清空canvas
- buffer: 指定待清空的缓冲区，位操作符OR(|)可以用来指定多个缓冲区
	- gl.COLOR_BUFFER_BIT
	- gl.DEPTH_BUFFER_BIT
	- gl.STENCIL_BUFFER_BIT

#### gl.clearColor(red, green, blue, alpha)：清空颜色缓存，并设置背景色
- red: 指定红色值（从0.0到1.0）
- green: 指定绿色值（从0.0到1.0）
- blue: 指定蓝色值（从0.0到1.0）
- alpha: 指定透明度值（从0.0到1.0）
- 如果任何值小于0.0或大于1.0，均会被阶段

#### gl.polygonOffset(factor, units)：指定每个顶点绘制后Z值上的偏移量

#### gl.clearDepth(depth)：清空深度缓冲区
- depth：深度值，从0.0到1.0，默认1.0

#### gl.clearStencil(s)：清空模板缓冲区
- s: 默认0

#### gl.createShader(type)：创建着色器
- type：着色器类型
	- gl.VERTEX_SHADER
	- gl.FRAGMENT_SHADER
- return：着色器对象

#### gl.deleteShader(shader)：删除着色器
- shader：待删除的着色器

#### gl.shaderSource(source)：设置着色器源代码
- source：源代码，文本信息

#### gl.compileShader(shader)：编译着色器
- shader：待编译的着色器

#### gl.getShaderParameter(shader, param)：获取着色器参数信息
- shader：待获取参数的着色器
- param：参数信息，参考全局常量-着色器参数

#### gl.getShaderInfoLog(shader)：获取shader指定的着色器信息日志
- shader：待获取信息日志的着色器

#### gl.attachShader(program, shader)：将shader指定的着色器分配给program指定的程序
- program：指定程序
- shader：指定着色器

#### gl.createProgram()：创建程序

#### gl.deleteProgram(program)：删除程序
- program：待删除的程序

#### gl.linkProgram(program)：连接program指定的程序中的着色器
- program：指定程序

#### gl.useProgram(program)：告知webgl系统绘制时使用program指定的程序
- program：指定待使用的程序

#### gl.getProgamParameter(program, param)：获取程序参数信息
- program：指定程序
- param：参数信息，参考全局常量-程序参数

#### gl.getProgamInfoLog(program)：连接program指定的程序中的信息日志
- program：指定程序

#### gl.drawArrays(mode, first, count)：执行顶点着色器，按照mode参数指定的方式绘制图形
- mode：指定绘制方式，参考全局常量-绘制模式
- first：指定从哪个顶点开始绘制，整数型
- count：指定绘制需要用到多少个顶点，整数型

#### gl.drawElements(mode, count, type, offset)：执行着色器，按照mode参数指定的方式，根据绑定到gl.ELEMENT_ARRAY_BUFFER的缓冲区的顶点索引绘制图像
- mode：指定绘制方式，参考全局常量-绘制模式
- count：指定绘制需要用到多少个顶点，整数型
- type：指定索引值数据类型
- offset：指定索引数组中开始绘制的位置

#### gl.getAttribLocation(program，name)：获取由name参数指定的attribute变量的存储位置
- program：程序对象
- name：变量名称
- return：-1或者≥0，存储位置

#### gl.getUniformLocation(program，name)：获取指定名称的uniform变量的存储位置
- program：程序对象
- name：变量名称
- return：变量位置|null

#### gl.readPixels(x, y, width, height, format, type, pixels)：从颜色缓冲区中读取指定的区域的像素值，并保存在指定的数组中
- x,y：指定颜色缓冲区中的矩形块左上角坐标
- width,height：指定矩形块的宽度和高度，以像素为单位
- format：指定像素值的颜色格式
- type：指定像素值的数据格式
- pixels：指定用来接收像素值数据的Uint8Array类型化数组

#### gl.blendFunc(src_factor, dst_factor)：通过参数指定进行混合操作的函数
- 混合后的颜色计算公式：<混合后颜色> = <源颜色> x src_factory + <目标颜色> x dst_factory
- src_factor：指定源颜色在混合后颜色中的权重因子
- dst_factor：指定目标颜色在混合后颜色中的权重因子

#### gl.depthMask(mask)：锁定或释放深度缓冲区的写入操作
- mask：指定是锁定深度缓冲区的写入操作（false），还是释放之（true）

#### gl.viewpoint(x, y, width, height)：设置绘制区域
- x, y：指定绘图区域的左上角，单位为像素
- width, height：指定绘图区域的宽度和高度

### 缓冲区API
负责处理缓冲区数据

#### gl.createBuffer()：创建缓冲区对象
- return：缓冲区对象

#### gl.deleteBuffer(buffer)：删除缓冲区对象
- buffer：待删除的缓冲区对象

#### gl.bindBuffer(target, buffer)：允许使用buffer表示的缓冲区对象并将其绑定到target表示的目标上
- target：参考全局变量-缓冲区target参数
- buffer：待绑定的缓冲区对象

#### gl.bufferData(target, data, usage)：开辟存储空间，向绑定在target上的缓冲区对象写入数据
- target：参考全局变量-缓冲区-target参数
- data：写入缓冲区对象的数据
- usage： 表示程序将如何使用存储在缓冲区对象中的数据，参考全局变量-缓冲区-usage参数

#### gl.enableVertexArray(location)：开启location指定的attribute指定的变量
- location：指定待分配attribute变量的存储位置

#### gl.disableVertexArray(location)：：关闭location指定的attribute指定的变量
- location：指定待分配attribute变量的存储位置

#### gl.createFramebuffer()：创建帧缓冲区对象

#### gl.deleteFramebuffer(framebuffer)：删除帧缓冲区对象
- framebuffer：待删除的帧缓冲区对象

#### gl.bindFramebuffer(target, framebuffer)：将指定的framebuffer绑定到target目标
- target：必须为gl.FRAMEBUFFER
- framebuffer：待绑定的帧缓冲区对象

#### gl.framebufferTexture2D(target, attachment, textarget, texture, level)：将指定的纹理对象关联到帧缓冲区
- target：必须为gl.FRAMEBUFFER
- attachment：关联类型
- textarget：纹理类型
- texture：指定关联的纹理对象
- level：指定为0（在使用MIPMAP纹理时指定纹理的层级）

#### gl.framebufferRenderbuff(target, attachment, renderbuffertarget, renderbuffer)：将渲染缓冲区关联到帧缓冲区
- target：必须为gl.FRAMEBUFFER
- attachment：关联类型
- renderbuffertarget：必须为gl.RENDERBUFFER
- renderbuffer：被关联的渲染缓冲区对象

#### gl.checkFramebufferStatus(target)：检查帧缓冲区对象的配置状态
- target：必须为gl.FRAMEBUFFER
- return：帧缓冲区检查状态

#### gl.createRenderbuffer()：创建渲染缓冲区对象

#### gl.deleteRenderbuffer(renderbuffer)：删除渲染缓冲区对象
- renderbuffer：待删除的渲染缓冲区对象

#### gl.bindRenderbuffer(target, renderbuffer)：将指定的renderbuffer绑定到target目标
- target：必须为gl.RENDERBUFFER
- renderbuffer：待绑定的渲染缓冲区对象

#### gl.renderbufferStorage(target, internalformat, width, height)：创建并初始化渲染缓冲区的数据区
- target：必须为gl.RENDERBUFFER
- internalformat：指定渲染缓冲区中的数据格式
- width：渲染缓冲区的宽度，单位为像素
- height：渲染缓冲区的高度，单位为像素

### 纹理API
#### gl.createTexture()：创建纹理对象以存储纹理图像
- return：纹理对象

#### gl.deleteTexture(texture)：使用texture删除纹理对象
- texture：待删除的纹理对象

#### gl.pixelStorei(pname, param)：使用pname和param指定的方式加载得到的图像
- pname：可选值UNPACK_FLIP_Y_WEBGL或UNPACK_PREMULTIPLY_ALPHA_WEBGL
- param：指定非0（true）或0（false）。必须为整数

#### gl.activeTexture(texUnit)：激活texUnit指定的纹理单元
- texUnit：指定准备激活的纹理单元：TEXTURE0…TEXTURE7。最后数字表示纹理单元编号

#### gl.bindTexture(target, texture)：开启texture指定的纹理对象，并将其绑定到target上
- target：纹理类型，TEXTURE_2D或TEXTURE_CUBE_MAP
- texture：待绑定的纹理单元

#### gl.generateMipmap(target)：指定将生成其mipmap的活动纹理的绑定点（目标）
- target：纹理类型，TEXTURE_2D或TEXTURE_CUBE_MAP

#### gl.texParameteri(target, pname, param)：将param的值赋给绑定目标的纹理对象的pname参数上
- target：纹理类型，TEXTURE_2D或TEXTURE_CUBE_MAP
- pname：纹理参数
- param：纹理参数值

#### gl.texImage2D(target, level, internalformat, format, type, image)：将image指定的图像分配给绑定到目标上的纹理对象
- target：纹理类型，TEXTURE_2D或TEXTURE_CUBE_MAP
- level：传入0，该参数是为金字塔纹理的
- internalformat：图像的内部格式
- format：纹理数据的格式，必须使用与internalformat相同的值
- type：纹理数据的类型
- image：包含纹理图像的image对象

### 数据传输API
复杂将js生成的数据传输给着色器

#### 函数命名规范
- <基础函数名><参数个数><参数类型>
- <gl.vertexAttrib><1|2|3|4><f|fv>
- <gl.uniform><1|2|3|4><f|i|fv|iv>
- <gl.uniformMatrix><2|3|4><fv>

### 发送float数据给指定location的atrribute变量
#### gl.vertexAttrib1f(location, v0)
#### gl.vertexAttrib2f(location, v0, v1)
#### gl.vertexAttrib3f(location, v0, v1, v2)
#### gl.vertexAttrib4f(location, v0, v1, v2, v3)
#### gl.vertexAttrib1fv(location, value)
#### gl.vertexAttrib2fv(location, value)
#### gl.vertexAttrib3fv(location, value)
#### gl.vertexAttrib4fv(location, value)

#### gl.vertexAttribPointer(location, size, type, normalized, stride, offset)：将绑定到gl.ARRAY_BUFFER的缓冲区对象分配给由location指定attrib变量
- location：指定待分配attribute变量的存储位置
- size：指定缓冲区中每个顶点的分量个数（1~4）
- type：指定数据格式，参考全局变量-数据格式
- normalized：传入true或false，表明是否将非浮点型的数据归一化
- stride：指定相邻两个顶点间的字节数，默认为0
- offset：指定缓冲区对象中的偏移量（以字节为单位），即attribute变量从缓冲区中的何处开始存储，默认为0

### 发送float数据给指定location的uniform变量
#### gl.uniform1f(location, v0)
#### gl.uniform2f(location, v0, v1)
#### gl.uniform3f(location, v0, v1, v2)
#### gl.uniform4f(location, v0, v1, v2, v3)
#### gl.uniform1fv(location, value)
#### gl.uniform2fv(location, value)
#### gl.uniform3fv(location, value)
#### gl.uniform4fv(location, value)

### 发送int数据给指定location的uniform变量
#### gl.uniform1i(location, v0)
#### gl.uniform2i(location, v0, v1)
#### gl.uniform3i(location, v0, v1, v2)
#### gl.uniform4i(location, v0, v1, v2, v3)
#### gl.uniform1iv(location, value)
#### gl.uniform2iv(location, value)
#### gl.uniform3iv(location, value)
#### gl.uniform4iv(location, value)

### 发送matrix数据给指定location的uniform变量
#### gl.uniformMatrix2fv(location, transpose, arary)
#### gl.uniformMatrix3fv(location, transpose, arary)
#### gl.uniformMatrix4fv(location, transpose, arary)



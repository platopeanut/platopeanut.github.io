
<span style="color:red;">以Shader为中心进行设计</span>

- Geometry
	- 输入顶点信息vertices和索引信息indices
		- 顶点信息包括：position，normal，texCoords
	- 维护VAO，VBO，EBO
	- 在render的时候绑定VAO和Drawcall
- Material
	- 接口类，每一个实现类需要维护自己的Shader
	- 所有实现类需要实现getShader方法
	- 在render的时候需要将特定材质涉及到的参数传递给shader（uniform），比如phong material中需要传递
		- ambient
		- diffuse
		- specular
		- shininess
- Transform
	- 维护position，rotation，scale
	- 根据平移旋转缩放计算得到model matrix
	- 在render的时候需要设置shader的model matrix
- Render Context
	- 一个结构体类，提供了渲染一个GameObject所必要的场景数据
	- 摄像机Camera
		- Position
		- View Matrix
		- Projection Matrix
	- 灯光数组List\<Light>
		- 比如Position，Intensity
- Game Object
	- 拥有：Geometry，Material，Transform
	- 持有来自Material.getShader的Shader引用
	- 在render方法中，需要传入一个RenderContext
		- 调用shader.use()
		- 设置RenderContext到shader中（uniform）
		- 依次调用Geometry，Material，Transform的render


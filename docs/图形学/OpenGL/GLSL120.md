## Data Types

基本数据类型：`float`、`bool`、`int`

向量：
- `vec{2,3,4}`：n个float
- `bvec{2,3,4}`：n个bool
- `ivec{2,3,4}`：n个int

矩阵：`mat2`、 `mat3`、 `mat4`

Samplers
Sampler是从**纹理图像**上根据**纹理坐标**进行采样的采样器
- `sampler1D`
- `sampler2D`
- `sampler3D`

用于立方体贴图
- `samplerCude`

用于阴影贴图：
- `sampler1DShadow`
- `sampler2DShadow`

其他类型：数组，结构体

## Attribute Variables

>If it is required to set variables per vertex then attribute variables must be used. In fact attribute variables can be updated at any time. Attribute variables can only be read (not written) in a vertex shader. This is because they contain vertex data, hence not applicable directly in a fragment shader (see the section on [varying](http://www.lighthouse3d.com/tutorials/glsl-12-tutorial/varying-variables/ "Varying Variables") variables).

程序输入给Vertex Shader中顶点的各种属性（位置，法线，颜色，纹理坐标等）的变量就是Attribute Variables。

现代GLSL已废弃，替代为：layout(location=x) in

## Varying Variables

>This type of variables, where the fragment receives interpolated, data are “varying variables”.

在Vertex Shader中定义要传给Fragment Shader的变量，这样Fragment Shader中可以获取这个变量，这就是Varying Variables。

现代GLSL已废弃，替代为：in/out


## Predefined

### Variables

GLSL has access to part of the OpenGL state.

| 变量名                       | 类型 | 描述                                                                                               |
| ---------------------------- | ---- | -------------------------------------------------------------------------------------------------- |
| gl_Position                  | vec4 | vertex shader需要输出的变量，他是经过MVP变换到裁剪空间上的位置                                     |
| gl_FragColor                 | vec4 | fragment shader需要输出的变量，片段的颜色值                                                        |
| gl_Vertex                    | vec4 | 输入给vertex shader的顶点坐标                                                                      |
| gl_ModelViewMatrix           | mat4 | 模型视图矩阵                                                                                       |
| gl_ProjectionMatrix          | mat4 | 投影矩阵                                                                                           |
| gl_ModelViewProjectionMatrix | mat4 | MVP变换矩阵                                                                                        |
| gl_Color                     | vec4 | 要想在fragment shader中使用他，需要在vertex shader中设置gl_FrontColor，具体请见Samples/ColorShader |
| gl_FrontColor                | vec4 |                                                                                                    |
| gl_BackColor                 | vec4 |                                                                                                    |
| gl_NormalMatrix              | mat4 | 变换法线gl_Normal的矩阵                                                                            |
| gl_Normal                    | vec4 | 顶点法线                                                                                                   |


### Functions

| 函数声明               | 描述                                                                                         |
| ---------------------- | -------------------------------------------------------------------------------------------- |
| vec4 ftransform(void); | 等于对gl_Vertex应用MVP变换。不仅可以保证获得最佳性能，还可以保证结果始终与使用固定功能时相同 |

## Samples

### Hello World

VERT

~~~c
#version 120

void main() {
	// 写法1
	gl_Position = gl_ProjectionMatrix * gl_ModelViewMatrix * gl_Vertex;
	// 写法2
	gl_Position = gl_ModelViewProjectionMatrix * gl_Vertex;
	// 写法3（最快）
	gl_Position = ftransform();
}
~~~

FRAG

~~~cpp
#version 120

void main() {
	gl_FragColor = vec4(0.4,0.4,0.8,1.0);
}
~~~


### Color Shader

VERT

~~~c
#version 120

void main() {
	gl_FrontColor = gl_Color;
	gl_Position = ftransform();
}
~~~

FRAG

~~~c
#version 120

void main()
{
	gl_FragColor = gl_Color;
}
~~~

### Blinn-Phong

~~~c
#version 120

varying vec3 vNormal;
varying vec3 vPosition;

void main() {
	vPosition = vec3(gl_ModelViewMatrix * gl_Vertex);
	vNormal = vec3(gl_NormalMatrix * gl_Normal);
	gl_Position = ftransform();
}
~~~

~~~c
#version 120

uniform vec3 uLightPosition;
uniform vec3 uLightColor;
uniform vec3 uAmbientColor;
uniform vec3 uDiffuseColor;
uniform vec3 uSpecularColor;
uniform float uShininess;

varying vec3 vNormal;
varying vec3 vPosition;

void main()
{
    // 计算顶点法线和光源方向的单位向量
    vec3 normal = normalize(vNormal);
    vec3 lightDirection = normalize(uLightPosition - vPosition);

    // 计算视线方向的单位向量
    vec3 viewDirection = normalize(-vPosition);

    // 计算反射光线方向的单位向量
    vec3 reflectDirection = reflect(-lightDirection, normal);

    // 计算漫反射光照强度
    float diffuseIntensity = max(dot(normal, lightDirection), 0.0);

    // 计算镜面反射光照强度
    float specularIntensity = pow(max(dot(reflectDirection, viewDirection), 0.0), uShininess);

    // 计算最终的光照强度
    vec3 lighting = uAmbientColor + uDiffuseColor * diffuseIntensity + uSpecularColor * specularIntensity;

    // 将光照强度与物体颜色相乘得到最终的片段颜色
    gl_FragColor = vec4(uDiffuseColor * lighting, 1.0);
}
~~~

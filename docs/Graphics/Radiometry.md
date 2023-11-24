
## Motivation

 
- 在Blinn-Phong模型中，光的强度（Light Intensity）简化为一个数（比如10），没有单位。
- 整个模型太理想，比如折射总是精确的；太简单，比如光线在传播的过程中能量衰减（比如80%）

辐射度量学就是一个精准的给我们光的一系列物理量的方法
把光精确的定义出来，包括物体的表面如何与光进行作用精确的描述出来，光线传播方法精确描述等等

精确定义了一系列空间中的光的属性（Spatial Properties of Light），仍然是基于几何光学（假定光沿直线传播，不考虑光的波动性）

定义了光照的若干属性（物理量）：Radiant flux，intensity，irradiance，radiance

**Perform lighting calculation in a physical correct manner.**

## Radiant Energy and Flux (Power)

>**Radiant energy (能量)** is the energy of electromagnetic radiation. It is measured in units of joules, and denoted by the symbol:

$$
Q [J = Joule]
$$

>**Radiant flux (power，功率)** is the energy emitted, reflected, transmitted or received, per unit time

$$
\Phi \equiv \frac{dQ}{dt} [W = Watt] [lm = lumen]^*
$$

在光学中描述一个物体的功率可以用流明（lumen）来表示。
Flux也可以定义为单位时间辐射到特定感光器（sensor）上的能量
Flux可以定义灯泡的亮度（Flux越大灯越亮）

## Radiant Intensity

Light Emitted From A Source

>The radiant (luminous) intensity is the power per unit **solid angle (立体角)** emitted by a point light source.

$$ I(w) \equiv \frac{d\Phi}{d\omega} $$

$$ [\frac{W}{sr}][\frac{lm}{sr}=cd=candela] $$

The candela is one of the 7 SI base units.

### Solid Angle

$$ \Omega = \frac{A}{r^2} $$

Sphere has $4\pi$ steradians. 

单位立体角定义：（$\theta$ 和$\phi$ 各自变化一点点，立体角的变化）

$$ dA = (rd\phi)~(rsin\theta~d\theta) = r^2sin\theta~d\theta ~d\phi
$$

$$
d\omega = \frac{dA}{r^2} = sin\theta~d\theta ~d\phi
$$

整个球的立体角：

$$
\Omega = \int_{S^2}d\omega = \int_0^{2\pi}\int_0^{\pi}sin\theta ~d\theta~d\phi=4\pi
$$

$\omega$ as a direction vector !

在辐射度量学中，如果要表示三维空间中的一个方向，我们会用$\omega$来表示（$\omega$可以用$\theta$和$\phi$来定义），并且可以计算他对应的单位立体角（微分立体角）。

因此假设一个点光源向四周均匀发出能量：

$$
\Phi = \int_{S^2}Id\omega=4\pi I
$$

$$
I = \frac{\Phi}{4\pi}
$$
## Irradiance

Light Falling On A Surface

>The irradiance is the power per (perpendicular/projected，表面要保证入射光线相垂直的方向，如果不垂直则要进行投影，类似于phong模型的diffuse也要考虑光线和法线之间的$cos\theta$) unit area incident on a surface point.

$$E(x) \equiv \frac{d\Phi(x)}{dAcos\theta}$$

$$
[\frac{W}{m^2}][\frac{lm}{m^2}=lux]
$$

因此，点光源在向外传播的过程中，Intensity一直不变，衰减的是Irradiance，衰减速度是$r^2$

## Radiance

Light Travelling Along A Ray
与光线的关系紧密结合

>The radiance (luminance) is the power emitted, reflected, transmitted or received by a surface, per unit solid angle（单位立体角）, per projected unit area（单位投影后的面积）.

$$
L(p, \omega) \equiv \frac{d^2\Phi(p, \omega)}{d\omega~dAcos\theta}
$$

$$
[\frac{W}{srm^2}][\frac{cd}{m^2}=\frac{lm}{sr m^2}=nit]
$$

光线就是从某一个微小的表面辐射出来，此时也是四面八方的，再分析某一个特定的方向。

联系上文的Intensity和Irradiance，他们都是一次微分，而Radiance是两次微分。所以：
- Radiance：Irradiance per solid angle（Radiance在Irradiance的基础上增加了**方向性**）
	- Irradiance就是dA的上的能量
	- 从各个方向到达dA的Radiance的能量积分积起来就是Irradiance
- Radiance：Intensity per projected unit area

入射进来：某一个方向接收到的能量，用Irradiance和Radiance关系来解释。
从一个小的面，辐射出去，往哪个方向去，用Intensity来解释。

## BRDF

BRDF：Bidirectional Reflectance Distribution Function（双向反射分布函数）

$$
f_r(\omega_i \to \omega_r) = \frac{dL_r(\omega_r)}{dE_i(\omega_i)} = \frac{dL_r(\omega_r)}{L_i(\omega_i)cos\theta_id\omega_i}~[\frac{1}{sr}]
$$

一个Radiance以$\omega_i$的方向入射到$dA$，累积成Irradiance。$dA$又要向四面八方发射能量，对于要发射的方向$\omega_r$，要发射出多少能量呢，或者说这个能量占入射总能量的多少呢，这是一个比例，因此BRDF描述了这样的一个比例，或者说能量分布。

一个入射光打进来，会向四面八方进行发射。至于表现为漫反射还是镜面反射，本质上是BRDF不同。

BRDF描述了光线和物体是如何作用的，决定了物体不同的材质！！！


## The Reflection Equation

$$
L_r(p, \omega_r) = \int_{H^2}f_r(p, \omega_i \to \omega_r) L_i(p, \omega_i) cos\theta_i d\omega_i
$$

反射方程指出，对于任何一个着色模型，无非就是：对于一个出射方向上的光的能量，等于从四面八方打到$dA$的光线的能量的分别乘上BRDF这个比例然后再累加起来的能量。

困难：这个方程告诉我们当我们在进行追踪的反射光线时候，对应的顶点的入射光线会来自四面八方，可不仅仅只有光源，也有可能是其他物体间接反射过来的。也就是说光线不止弹射一次（因此反射方程是一个Recursive Equation）

## The Rendering Equation

渲染方程是对反射方程的补充，他考虑了一个物体会自己发光的情况

$$
L_o(p, \omega_o) = L_e(p, \omega_o) + \int_{\Omega^+}f_r(p, \omega_i ,\omega_o) L_i(p, \omega_i) (n \cdot \omega_i) d\omega_i
$$

这个公式我们假定：

- 所有的方向（入射，出射）都是朝外的（outwards）
- 所有的入射方向都是半球（半球用 $\Omega^+$ 或 $H^2$ 来表示）
	- 我们忽略一个从物体底下里面打到顶点的情况（忽略下半球），之前的Blinn-Phong我们的代码
~~~cpp
max(cos_theta, 0)
~~~

所有的（限制在物体表面上的）光线传播都可以用这个公式来总结
发明这个方程的论文名称就是The Rendering Equation（名字越短，事情越大）


## Global Illumination

将渲染方程简写为：

$$
L = E + KL
$$

K：反射操作符

所有物体辐射出来的所有能量 = 光源本身辐射出的能量 + 辐射出来的能量被反射之后得能量。这么写的目的是为了解渲染方程（解出L）
下面$(I - K)^{-1}$用到了泰勒展开

$$
\begin{aligned}
IL - KL &= E \\
(I - K)L &= E \\
L &= (I - K)^{-1}E \\
L &=(I + K + K^2 + K^3 + \dots)E \\
L &= E + KE + K^2E + K^3E + \dots
\end{aligned}
$$

我们可以把最后看到的能量分解成：

|   项   | 弹射次数 |      描述      |
|:------:|:--------:|:--------------:|
|  $E$   |    0     | 直接看到的光源 |
|  $KE$  |    1     |    直接光照    |
| $K^2E$ |    2     |    间接光照    |
| $K^3E$ |    3     | 更多的间接光照               |

全部项加起来就是全局光照（Global Illumination）

光栅化可以做什么：

- 光源本身自己投影到屏幕上（0次）
- 着色：直接光照（1次）

后面得部分光栅化就比较难做！光线追踪很容易做间接光照
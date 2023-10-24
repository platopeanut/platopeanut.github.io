
<div style="color:red;">Material == BRDF</div>

### Diffuse / Lambertian Material BRDF

### Glossy material BRDF

### Ideal reflective / refractive material (BSDF*)

### Perfect Specular Reflection BRDF

Specular Refraction

Snell’s Law

Fresnel Reflection / Term
## Microfacet Material BRDF

### Microfacet Theory

微表面模型

假设：只要当我离物体足够远，很多微小的东西我们看不到，我们能看到的是这些微小的东西对表面的一个作用，与光作用的一个整体效应。

假设：物体表面是粗糙的，从远处看表面是平的（macrosurface），从近处看可以看到一个个微表面（microsurface）

Rough surface：

- Macroscale: flat & rough 平面且粗糙（粗糙是指材质是粗糙的）
- Microscale: bumpy & specular 凹凸不平且每一个微表面都是一个标准的镜面

**微表面认为：从远处看看到的是材质和外观，从近处看看到的是几何。**

因此，材质和几何具有很强的关系

**每一个微表面都是一个微小的镜面，每一个微表面都有自己的法线**

我们可以研究微表面的法线形成的**分布**

**The distribution of microfacets‘ normals.**

- Concentrated（集中） -> glossy
- Spread（发散） -> diffuse

我们可以表面的粗糙程度，用微表面的法线分布来表示。

### Microfacet BRDF

$$
f(i, o) = \frac{F(i, h)G(i, o, h)D(h)}{4(n, i)(n, o)}
$$

Fresnel term （F）：总共有多少能量被反射

Shadowing Masking Term（G，几何项）：微表面之间可能会发生互相遮挡，造成部分微表面不发挥作用，导致不应该反射这么多的能量，在光线与表面十分平行的时候（grazing angle）最容易发生，因此会看到球的边缘会特别亮。所以shadowing masking term就是为了修正这个东西，削减这个能量，就不会那么亮。

Distribution of Normals（D）：给定入射方向和出射方向，到底有多少微表面可以做到这个反射。就是看有多少微表面与半程向量half vector（入射和出射中间的向量）方向一致。这一项用来表示表面是集中还是发散。（决定性分布）

微表面模型太强大，可以描述的东西特别多！具有统治力，PBR一定会使用微表面模型。

微表面的缺点，比如 diffuse项很少

微表面模型只是一个统称，有很多微表面的模型，但都遵守这一套理论。

区分材质的一类方法

- 各向同性材质（Isotropic）：微表面不存在一定的方向性
- 各项异性材质（Anisotropic）
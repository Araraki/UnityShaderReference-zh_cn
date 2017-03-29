# 着色器*(Shader)*参考

Unity中的着色器可以用三种不同的方式编写：

* <u>**表面着色器**</u> *surface shaders*


* <u>**顶点和片段着色器**</u> *vertex and fragment shaders*


* **固定函数着色器** *fixed function shaders*

<u>着色器教程</u>可以指导您为您的需求选择正确的着色器类型。
无论你选择哪种类型，着色器代码实际上总是包裹在一种称为 *ShaderLab* 的语言中，*ShaderLab* 用于组织着色器结构。 它看起来像这样：

    Shader "MyShader"
    {
    	Properties
    	{
        	_MyTexture ("My Texture", 2D) = "white" { }
        	// other properties like colors or vectors go here as well
        	// 其他资源，如颜色或向量等
    	}
    	SubShader
    	{
        	// 你的 shader 内容
        	// - 表面着色器 surface shader 或
        	// - 顶点和片段着色器 vertex and fragment shaders 或
        	// - 固定函数着色器 fixed function shaders
    	}
    	SubShader
    	{
        	// 更简单的SubShader版本
         	// 使其可以在较旧的显卡上运行
    	}
    }


我们建议您首先阅读下面列出的 <u>ShaderLab 语法</u>的一些基本概念，然后继续阅读关于表面着色器或顶点和片段着色器的其他部分。 由于固定函数着色器仅使用 ShaderLab 编写，您将在 ShaderLab 参考本身中找到有关它们的更多信息。

下面的参考包括大量不同类型的着色器的示例。 对于更多的表面着色器的示例，您可以从参考资料里获取 Unity <u>内置着色器源代码</u>。 Unity 的<u>图像效果</u>包包含了很多有趣的顶点和片段着色器。

阅读着色器参考，并看看<u>着色器教程</u>！


# Writing Surface Shaders
???????
# Writing vertex and fragment shaders
???????


# ShaderLab 语法

Unity 中的所有 <u>Shader</u> 都使用叫做 “ShaderLab” 的声明性语言编写。 在文件中，嵌套大括号描述了着色器的各种事物，例如哪些着色器属性应显示在材质检查器中；哪些硬件回调函数需要处理；使用什么样的混合模式等。它们和实际的“着色器代码”写在同一着色器文件内的 `CGPROGRAM` 片段中（参见<u>表面着色器</u>和<u>顶点和片段着色器</u>）。

这个页面和子页面描述了大括号内的 “ShaderLab” 的语法，`CGROGRAM` 片段是使用 HLSL/Cg 规则编写，可以参见<u>这些文档</u>。

## 语法 *Syntax*

`Shader "name" { [Properties] Subshaders [Fallback] [CustomEditor] }`

定义Shader。此 Shader 将显示在名为 **name** 下列出的Material Inspector 中。 着色器可以按需要定义显示在 Material Inspector 中的 **Properties** 列表。 在这之后会有一个 SubShaders 列表和可选择的 fallback 或自定义编辑器声明。

#### 说明

##### Properties（资产）

Shader 可以有一个<u>资产*properties*</u>列表。任何在 Shader 中的声明的 properties 都将显示在 Unity 内的<u>Material Inspector</u> 内。典型的 properties 如对象颜色*color*，纹理*texture*，或者能被 Shader 使用的任意值。

##### SubShaders & Fallback

每个 Shader 都是由一个（至少有一个元素的） <u>sub-shaders</u> 列表组成的，当加载 shader 时，Unity 将通过 subshaders 列表，匹配能够支持用户机器的第一个 subshader ，如果没有任何一个 subshaders 能够匹配，Unity 将尝试使用 <u>fallback shader</u> 。

不同的图形卡有不同的能力，这为游戏开发者带来了一个永恒的问题; 你希望你的游戏在最新的硬件上看起来很棒，但不希望它只能在3%的玩家的电脑上运行。 这就是为什么需要 subshaders 。创建一个具有所有梦幻般图形效果的 subshader ，然后为较旧的显卡添加更多的 subshaders 。 这些 subshadders 可能以较慢的方式实现你想要的效果，或者他们可能选择不实现一些细节。

Shader “level of detail”(LOD) 和 “Shader Replacemement”(着色器替换) 是也基于 subshaders 的两种技术，细节参见 <u>Shader LOD</u> 和 <u>Shader Replacemement</u>。

#### 示例

这是一个可能出现最简单的 shader ：

	// colored vertex lighting
	Shader "Simple colored lighting"
	{
		// 单个名为 color 的 property
	    Properties
	    {
	    	_Color ("Main Color", Color) = (1,.5,.5,1)
		}
		// 定义一个 SubShader
		SubShader
		{
			// subshader 里的一个 pass
			Pass
			{
				// 使用固定功能每顶点光照(fixed function per-vertex lighting)
				Material
				{
					Diffuse [_Color]
				}
				Lighting On
			}
		}
	}

这个 shader 定义了颜色属性 **_Color** （在材质检查器中显示为主颜色），默认值为**(1,0.5,0.5,1)**。 然后定义单个 subshade r。 subshader 由一个 <u>Pass</u> 打开固定功能顶点照明并为其设置基本材质。

查看更复杂的示例，参见 <u>Surface Shader Examples</u> 或 <u>Vertex and Fragment Shader Examples</u> 。



# ShaderLab Syntax: Properties

shaders 能够定义一个参数列表供设计师在 <u>Material Inspector</u> 中调整，<u>shader 文件</u>通过 Properties 块来定义它们。

## 语法 *Syntax*

### Properties

	Properties { Property [Property ...] }

定义Properties块。 内括号的多个属性定义如下。

#### 数字和滑动条	*Number and Sliders*

	name ("display name", Range (min, max)) = number
	name ("display name", Float) = number
	name ("display name", Int) = number

定义具有初始值的数字（标量）property 。 `Range`可使其显示为 min 和 max 之间的滑块。

#### 颜色和向量 *Colors and Vectors*


	name ("display name", Color) = (number,number,number,number)
	name ("display name", Vector) = (number,number,number,number)

定义具有 RGBA 初始值的颜色 property 或4维向量 property 。 颜色 property 将有一个颜色选择器，并根据颜色空间根据需要进行调整（请参阅<u>着色器程序中的属性</u>）。矢量属性显示为四个数字字段。

#### 纹理 *Texture*

	name (“display name”，2D) = "defaulttexture"{}
	name (“display name”，Cube) = "defaulttexture"{}
	name (“display name”，3D) = "defaulttexture"{}

分别定义<u>2D Texture</u>、<u>cubemap</u> 或 <u>3D(Volume)</u> property。

### 说明

shader 中的每个属性都由 **name** 引用（在Unity中，通常使用下划线作为 shader property 命名的开头）。属性将显示在材质检查器中作为显示名称。 对于每个属性，在等号后面给出默认值：

* 对于 *Range* 和 *Float* 属性，它只是一个数字，例如 13.37。


* 对于颜色和向量属性，它是括号中的四个数字，例如 (1, 0.5, 0.2, 1)。


* 对于 2D 纹理，默认值为空字符串或内置默认纹理之一：
  * “white”	(RGBA: 1, 1, 1, 1)
  * “black”    (RGBA: 0, 0, 0, 0)
  * “gray”      (RGBA: 0.5, 0.5, 0.5, 0.5)
  * “bump”   (RGBA: 0.5, 0.5, 1, 0.5)
  * “red”        (RGBA: 1,0,0,0)


* 对于非2D纹理(Cube, 3D, 2DArray)，默认值为空字符串。当材质没有分配Cubemap / 3D / Array纹理时，使用灰色的(RGBA：0.5, 0.5, 0.5, 0.5)。

在之后的着色器的固定函数部分中，可以使用方括号中的属性名称访问属性值：**\[name]**。 例如，您可以通过声明两个整数属性（称为“SrcBlend”和“DstBlend”），然后用 <u>Blend Command</u> 使用它们，通过材质属性驱动混合模式：`Blend [_SrcBlend] [_DstBlend]`。

property 块中的 shader 参数会被序列化为<u>材质</u>数据。 实际上<u>shader程序</u>可以有更多参数（如矩阵，向量和浮点数），这些参数在运行时从代码中设置在材质上，但如果它们不是属性块的一部分，那么它们的值将不会被保存。 这对于完全由脚本代码驱动的值（使用Material.SetFloat和类似函数）很有用。

#### Property属性和Drawer*(Property attributes and drawers)*

在任何 Property 前，可以指定方括号中的可选属性。这些是由Unity识别的属性，或者它们可以指示您自己的MaterialPropertyDrawer类，以控制如何在材质检查器中呈现它们。 Unity识别的属性：

* [HideInInspector] - 不在 Material Inspector 中显示属性值。


* [NoScaleOffset] - Material Inspector 将不显示具有此属性的纹理贴图的纹理 tiling/offset 字段。


* [Normal] - 表示纹理属性期望法线贴图。


* [HDR] - 指示纹理特性期望高动态范围（HDR）纹理。


* [Gamma] - 表示在UI中将浮点/向量属性指定为sRGB值（就像颜色一样），并且可能需要根据所使用的颜色空间进行转换。请参阅着色器程序中的属性。
* [PerRendererData] - 表示纹理属性将以MaterialPropertyBlock的形式来自每个渲染器数据。材质检查器会更改这些属性的纹理槽UI。


### 示例

	// properties for a water shader
	Properties
	{
		_WaveScale ("Wave scale", Range (0.02,0.15)) = 0.07 		// sliders
		_ReflDistort ("Reflection distort", Range (0,1.5)) = 0.5
		_RefrDistort ("Refraction distort", Range (0,1.5)) = 0.4
	    _RefrColor ("Refraction color", Color) = (.34, .85, .92, 1) // color
	    _ReflectionTex ("Environment Reflection", 2D) = "" {} 		// textures
	    _RefractionTex ("Environment Refraction", 2D) = "" {}
	    _Fresnel ("Fresnel (A) ", 2D) = "" {}
	    _BumpMap ("Bumpmap (RGB) ", 2D) = "" {}
	}


### 纹理property配置 (5.0后删除)

在 Unity 5 之前，纹理属性可以在花括号块中具有选项。例如：`TexGen CubeReflect`。 这些是用于控制固定函数纹理坐标生成。 此功能在5.0中已删除; 如果你需要texgen你应该写一个<u>顶点着色器</u>。 有关示例，请参阅<u>实现固定函数TexGen页面</u>。



# ShaderLab Syntax: SubShader

Unity中的每个着色器都包含一个subshaders列表。 当Unity必须显示网格时，它将找到要使用的shader，并选择能够在用户的显卡上运行的第一个subshader。

## 语法 *Syntax*

`Subshader { [Tags][CommonState] Passdef [Passdef ...] }`

将subshader定义为可选标记，包含 CommonState 和 Pass 定义列表。

## 说明
subshader 定义了<u>渲染 Pass</u> 的列表，并且可选地设置所有 Pass 共有的任何 state 。 此外，可以为 subshader 设置特定标签。

当 Unity 选择了要渲染哪个 subshader 时，它要为每个定义过的 Pass 都渲染一个对象（更有可能是由于光的相互作用）。 由于对象的每次渲染都是一次昂贵的操作，因此您希望以尽可能少的遍数来定义着色器。当然，有时在某些图形硬件上，所需的效果不能在单次 Pass 中完成；那么你别无选择，只能使用多个 Pass。

每个 Pass 定义都可以是 <u>regular Pass</u> ， <u>Use Pass</u> 或 <u>Grab Pass</u>。

在 Pass 定义中允许的任何语句也可以出现在Subshader块中。 这将使所有 Pass 使用此“共享”状态。

## 示例

	// ...
	SubShader
	{
		Pass
		{
		    Lighting Off
	    	SetTexture [_MainTex] {}
		}
	}
	// ...

这个 subshader 定义了单个 Pass ，关闭所有光照并只显示 **_MainTex** 贴图的网格。



# SubShader: Pass

Pass 块会使 GameObject 的几何体(geometry)被渲染一次。

## 语法 *Syntax*
	Pass{ [Name and Tags] [RenderSetup]}
基本的 Pass 命令包含了一个渲染状态设置命令的列表。
## Name 和 Tag
一个 Pass 可以定义它的 <u>Name</u> 和任意数量的 <u>Tag</u> 。它们都是将 Pass 的目的传递给渲染引擎的键值对字符串。
## 渲染状态设置 *Render state set-up*
Pass 设置图形硬件的各种状态，例如是否应该打开 Alpha 混合(alpha blending)或者是否应该使用深度测试(depth testing)。

命令如下：

### Cull
	Cull Back | Front | Off
设置多边形剔除模式。
### ZTest
	ZTest (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always)
设置深度缓冲(depth buffer)测试模式。
### ZWrite
	ZWrite On | Off
设置深度缓冲(depth buffer)写模式。
### Offset
	Offset OffestFactor, OffsetUnits
设置 Z 缓冲(Z buffer)偏移。详细内容参见文档 <u>Cull and Depth page</u> 。
### Blend
	Blend sourceBlendMode destBlendMode
	Blend sourceBlendMode destBlendMode, alphaSourceBlendMode alphaDestBlendMode
	BlendOp colorOp
	BlendOp colorOp, alphaOp
	AlphaToMask On | Off
设置 alpha 混合(blending)，alpha 操作(operation)和 alpha 到覆盖(alpha-to-coverage)模式。详细内容参见 <u>Blending</u> 。
### CollorMask
	CollorMask RGB | A | 0 | R,G,B,A 的任何组合
设置色彩通道写入遮罩，ColorMask 0时关闭所有颜色通道渲染。默认模式是写入所有通道(RGBA)，但对于某些特殊效果，你可能想保留某些通道未修改，或着完全禁用颜色写入。

当使用多渲染目标(Multiple Render Target - MRT)渲染时，可以通过在结尾处添加索引(0-7)为每个渲染目标设置不同的颜色掩码。例如， `ColorMask RGB 3` 将使渲染目标 #3 只写入 RGB 通道。

## 传统固定函数着色器(Fixed-function Shader)命令
许多命令是用来写传统的"固定函数样式(Fixed-function style)" shader 的。这些都被视为已弃用的功能，因为编写 <u>Surface Shader</u> 或 <u>Shader 程序</u>允许更多的灵活性。不过，对于非常简单的 Shader ，以固定函数着色器来编写它们有时会更容易，所以在此提供命令。注意，如果你不使用固定函数着色器的话，以下指令可忽视。
### 固定函数 Lighting 和 Material
	Lighting On | Off
	Material { Material Block }
	SparateSpecular On | Off
	Color Color-value
	ColorMaterial AmbientAndDiffuse | Emission
所有的这些指令控制固定函数的每个顶点照明：把它们打开，设置 Material 颜色，打开镜面高光，提供默认的颜色（如果顶点照明关闭），并控制网格顶点颜色如何影响照明。相关细节请阅读 <u>Materials</u> 。
### 固定函数 Fog
	Fog { Fog Block }
设置固定函数 Fog 参数。更多细节参见 <u>Fogging</u>。
### 固定函数 AlphaTest
	AlphaTEst (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always) CutoffValue
开启固定功能 alpha testing。更多细节参见 <u>alpha testing</u>。
### 固定函数 Texture combiners
在渲染状态设置好后，使用 <u>SetTexture</u> 指令来指定多个 Texture 及其组合模式(combining mode)。
	SetTexture textureProperty { combine options }
## 说明
Shader Pass 以多种方式与 Unity 的渲染管线交互，例如，Pass 可以指定它应该只被用于<u>延迟着色(deferred shading)</u>使用的 <u>Tag</u> 指令。某些 Pass 也可以在同一 GameObject 上执行多次，例如，在正向渲染(forward rendering)中 "ForwardAdd" Pass 将根据有多少光源影响 GameObject 而被执行多次。更多信息请阅读<u>渲染管线(Render Pipeline)</u>。

## Pass: Culling & Depth Testing
![](https://docs.unity3d.com/uploads/SL/PipelineCullDepth.png)
Culling 是一种不渲染没有面向观察者多边形的优化。所有的多边形都有前后两面，Culling 利用了大多数对象都符合的一个事实：如果你有一个立方体，你永远不会看到背向你的那一面（总有一个面是朝向你前方的），所以我们不需要绘制背向你的那一面。术语叫做：背面剔除(Backface Culling)。

使渲染看起来正确的另一个功能是深度测试(Depth testing)。深度测试确保只有最接近的表面的对象在场景中绘制。
### 语法 *Syntax*
#### Cull
	Cull Back | Front | Off
控制多边形的哪些面被剔除（不绘制）。
* **Back** 不绘制多边形背向观察者的面（默认）。
* **Front** 不绘制多边形面向观察者的面。用来把对象内外翻转。
* **Off** 关闭剔除 - 所有的面都会被绘制。用来制作特效。
#### ZWrite
	ZWrite On | Off
控制是否将来自对象的像素写入深度缓冲depth buffer（默认为开）。如果你绘制实体对象请保持此状态，如果想绘制半透明效果，请切换到*ZWrite Off* 。更多信息参见下面。
#### ZTest
	ZTest Less | Greater | LEqual | GEqual | Equal | NotEqual | Always
如何进行深度测试。默认值为 LEqual (从或在远处绘制对象，如同已有对象；隐藏其后面的对象)
#### Offset
	Offset Factor, Units
允许你用两个参数来指定深度偏移(depth offset)：*factor* 和 *units*。factor 相对于多边形的 X 或 Y 缩放最大 Z 斜率， units 缩放最小可分辨(minimum resolvable)深度缓冲区(depth buffer)值。这允许你强制绘制一个多边形在另一个的顶部，即便实际上他们的 position 是相同的。例如， `Offset 0, -1` 拉近多边形更接近相机，忽略多边形斜率；而当观察掠射角(gazing angle)时， `Offset -1, -1` 将拉近多边形。
### 示例
这个对象将只渲染它的背面：

	Shader "Show Insides"
	{
		SubShader
		{
			Pass
			{
				Material
				{
					Diffuse (1, 1, 1, 1)
				}
				Lighting On
				Cull Front
			}
		}
	}
尝试在一个 cube 上应用它，并注意观察你在它附近感觉到的几何图形错误。因为你只能看到这个 cube 的内部部分。
#### 具有深度的透明 shader *(Transparend shader with depth writes)*
通常<u>半透明shader(semitransparent shaders)</u>不写入到深度缓冲区(depth buffer)中。然而这可能造成绘制顺序错误，尤其是对于复杂的非凸网格(non-convex meshes)。如果你想要渐隐/渐显的网格就像下图这样，那么在渲染透明度之前使用填充深度缓冲区的 shader 可能是有用的。
![](https://docs.unity3d.com/uploads/Main/TransparentDiffuseZWrite.png)
半透明对象； 左: standard Transparent/Diffuse shader； 右: 写入深度缓冲区的 shader。

	Shader "Transparent/Diffuse ZWrite"
	{
		Properties
		{
			_Color ("Main Color", Color) = (1,1,1,1)
			_MainTex ("Base (RGB) Trans (A)", 2D) = "white" {}
		}
		SubShader
		{
			Tags {"Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Transparent"}
			LOD 200
			// 只渲染到深度缓冲区的额外 Pass
			Pass
			{
				ZWrite On
				ColorMask 0
			}

			// 粘贴 Transparent/Diffuse 的 Forward 渲染 Pass 到这里
			UsePass "Transparent/Diffuse/FORWARD"
		}
		Fallback "Transparent/VertexLit"
	}
#### 调试法线 *Debugging Normals*
下面的 shader 更有意思：首先我们用普通的顶点照明渲染对象，然后渲染背面时使用亮粉色。这个效果是所有需要你翻转法线的地方都会高亮。如果你看到物理控制(physically-controlled)的对象被任何网格“吸入”，请尝试这个 shader 。如果有任何粉色的部分可以被看到，这个部分将会拉入任何“不幸”的东西足够碰到它。

shader 如下：

	Shader "Reveal Backfaces"
	{
    	Properties
		{
        	_MainTex ("Base (RGB)", 2D) = "white" { }
    	}
    	SubShader
		{
       		// 渲染对象正面的部分。
        	// 我们使用一个简单的白色材质，并且应用主纹理。
        	Pass
			{
            	Material
				{
					Diffuse (1,1,1,1)
				}
            	Lighting On
            	SetTexture [_MainTex]
				{
                	Combine Primary * Texture
            	}
        	}

        	// 现在我们渲染背面的三角形
        	// 世界上最刺激的颜色：亮粉色！
        	Pass
			{
            	Color (1,0,1,1)
            	Cull Front
        	}
    	}
	}
#### 玻璃剔除 *Galss Culling*
控制剔除(Controlling Culling)对于大多数调试背面(debugging backfaces)是有用的。如果你有一个透明对象，你常想显示一个对象的背面。如果你的渲染没有任何的剔除(`Cull Off`)，你很可能得到一些后面的面重叠在前面的面上。

这里有一个简单的 shader ，可以工作在凸面对象上（球体、立方体、汽车挡风玻璃）。
	Shader "Simple Glass"
	{
    	Properties
		{
        	_Color ("Main Color", Color) = (1,1,1,0)
        	_SpecColor ("Spec Color", Color) = (1,1,1,1)
        	_Emission ("Emmisive Color", Color) = (0,0,0,0)
        	_Shininess ("Shininess", Range (0.01, 1)) = 0.7
        	_MainTex ("Base (RGB)", 2D) = "white" { }
    	}

    	SubShader
		{
        	// 我们通过在 subshader 中定义在许多 Pass 中使用的材质。
        	// 任何在这里定义的内容都将成为所有包含 Pass 的默认值
        	Material
			{
            	Diffuse [_Color]
            	Ambient [_Color]
            	Shininess [_Shininess]
            	Specular [_SpecColor]
            	Emission [_Emission]
        	}
        	Lighting On
        	SeparateSpecular On

        	// 设置 Alpha 混合
        	Blend SrcAlpha OneMinusSrcAlpha

        	// 渲染对象背向(back facing)的部分.
        	// 如果对象是凸的(convex)，他将总是比前面(front-faces)的面要远。
        	Pass
			{
            	Cull Front
            	SetTexture [_MainTex]
				{
                	Combine Primary * Texture
            	}
        	}
        	// 渲染对象朝向我们(facing us)的面。
        	// 如果对象是凸的，他将比背面(back-faces)要远。
        	Pass
			{
            	Cull Back
            	SetTexture [_MainTex]
				{
                	Combine Primary * Texture
            	}
        	}
    	}
	}

## Pass: 混合 *Blending*
混合*(Blending)* 用来制作透明对象。
![](https://docs.unity3d.com/uploads/SL/PipelineBlend.png)
当渲染图形时，在所有 shader 被执行并且所有纹理都被应用后，像素被写入屏幕。它们如何与已经存在的内容组合由混合(blend)命令控制。
### 语法 *Syntax*
`Blend Off`：关闭 blending（默认）

`Blend SrcFactor DstFactor`：配置并启用 blending。生成的颜色乘以 **SrcFactor**，已在屏幕上的颜色乘以 **DstFactor** 并且将两者相加。

`Blend SrcFactor DstFactor, SrcFactorA DstFactorA`：与上述相同，但使用不同的 factor 来混合 alpha 通道。

`BlendOp Op`：不将颜色加在一起，对他们进行不同的操作。

`BlendOp OpColor, OpAlpha`：与上述相同，但对于颜色（RGB）和alpha（A）通道使用不同的混合操作。

此外，你还可以设置上渲染目标(upper-rendertarget)混合模式。当时用多渲染目标(MRT, multiple render target)时上面的常规语法能为所有的渲染目标设置相同的混合模式。一下语法可以为各个渲染目标设置不同的混合模式，其中 `N` 是渲染目标索引(0..7)。此功能适用于大多数现在API/GPU(DX11/12, GLCore, Metal, PS4)：

* `Blend N SrcFactor DstFactor`
* `Blend N SrcFactor DstFactor, SrcFactorA DstFactorA`
* `Blend N SrcFactor DstFactor, SrcFactorA DstFactorA`
* `Blend N SrcFactor DstFactor, SrcFactorA DstFactorA`

`AlphaToMask On`：打开 alpha 覆盖(alpha-to-coverage)。当使用 MASS 时，alpha-to-coverage 会根据像素着色器结果 alpha 值按比例修改多重采样覆盖蒙版(multisample coverage mask)。这通常用于比正常 alpha test 更少的混叠轮廓(aliased outlines)；用于植被或者其他 alpha测试 shader。
### Blend 操作

以下是能被使用的 blend 操作：

|                         |                                      |
| :---------------------- | :----------------------------------- |
| **Add**                 | 将 source 和 destination 加在一起。     |
| **Sub**                 | 从 source 中减去 destination。        |
| **RevSub**              | 从 destination 中减去 source。        |
| **Min**                 | 使用 source 和 destination 中的最小值。 |
| **Max**                 | 使用 source 和 destination 中最大值。 |
| **LogicalClear**        | Logical operation: Clear (0) **DX11.1 only**. |
| **LogicalSet**          | Logical operation: Set (1) **DX11.1 only**. |
| **LogicalCopy**         | Logical operation: Copy (s) **DX11.1 only**. |
| **LogicalCopyInverted** | Logical operation: Copy inverted (!s) **DX11.1 only**. |
| **LogicalNoop**         | Logical operation: Noop (d) **DX11.1 only**. |
| **LogicalInvert**       | Logical operation: Invert (!d) **DX11.1 only**. |
| **LogicalAnd**          | Logical operation: And (s & d) **DX11.1 only**. |
| **LogicalNand**         | Logical operation: Nand !(s & d) **DX11.1 only**. |
| **LogicalOr**           | Logical operation: Or (s \| d) **DX11.1 only**. |
| **LogicalNor**          | Logical operation: Nor !(s \| d) **DX11.1 only**. |
| **LogicalXor**          | Logical operation: Xor (s ^ d) **DX11.1 only**. |
| **LogicalEquiv**        | Logical operation: Equivalence !(s ^ d) **DX11.1 only**. |
| **LogicalAndReverse**   | Logical operation: Reverse And (s & !d) **DX11.1 only**. |
| **LogicalAndInverted**  | Logical operation: Inverted And (!s & d) **DX11.1 only**. |
| **LogicalOrReverse**    | Logical operation: Reverse Or (s \| !d) **DX11.1 only**. |
| **LogicalOrInverted**   | Logical operation: Inverted Or (!s \| d) **DX11.1 only**. |

### Blend factors
以下所有属性在 **Blend** 命令中都适用于 SrcFactor 和 DisFactor。**Source** 指的是计算的颜色，**Destination** 是已经在屏幕上的颜色。如果 **BlendOp** 使用逻辑运算(Logical operation)，则忽略混合因子。

|                      |                                          |
| :------------------- | :--------------------------------------- |
| **One**              | 值为1的值 - 使用它让 source 或者 destination 其中一个颜色完全通过。 |
| **Zero**             | 值为0的值 - 使用它移除掉 source 或者 destination 的值。 |
| **SrcColor**         | 此阶段的值乘以源颜色的值。 |
| **SrcAlpha**         | 此阶段的值乘以源 Alpha 值。 |
| **DstColor**         | 此阶段的值乘以帧缓冲区(frame buffer)源颜色的值。 |
| **DstAlpha**         | 此阶段的值乘以帧缓冲区(frame buffer)源 Alpha 的值。 |
| **OneMinusSrcColor** | 此阶段的值乘以(1 - 源颜色) |
| **OneMinusSrcAlpha** | 此阶段的值乘以(1 - 源Alpha) |
| **OneMinusDstColor** | 此阶段的值乘以(1 - 目标颜色)。 |
| **OneMinusDstAlpha** | 此阶段的值乘以(1 - 目标Alpha)。 |

### 说明
以下是常见的 blend 类型：
	Blend SrcAlpha OneMinusSrcAlpha // 传统透明度 Traditional transparency
	Blend One OneMinusSrcAlpha // 自左乘透明度 Premultiplied transparency
	Blend One One // 增加 Additive
	Blend OneMinusDstColor One // 柔和增加 Soft Additive
	Blend DstColor Zero // 乘法 Multiplicative
	Blend DstColor SrcColor // 两倍乘法 2x Multiplicative

### Alpha混合(Alpha blending), alpha测试（Alpha testing）, alpha覆盖（alpha-to-coverage)
对于绘制大部分完全不透明或完全透明的对象，其中透明度由 Texture 的 Alpha 通道（例如叶，草，链栅栏等）定义，通常使用几种方法：
#### Alpha blending
![](https://docs.unity3d.com/uploads/SL/AlphaToMask-Blending.png)
标准的 alpha blending

这通常意味着对象必须被认为是“半透明的”，因此不能使用一些渲染特性（例如：延迟着色*deferred shading*，不能接收阴影）。 凹面(concave)或重叠的alpha混合对象经常也有绘制顺序问题。

通常，alpha-blender shader也要设置透明<u>渲染队列(render queue)</u>，并关闭深度写入。 所以 shader 代码看起来像：

	// 内部 SubShader
	Tags { "Queue"="Transparent" "RenderType"="Transparent" "IgnoreProjector"="True" }

	// 内部 Pass
	ZWrite Off
	Blend SrcAlpha OneMinusSrcAlpha

#### Alpha testing/cutout
![](https://docs.unity3d.com/uploads/SL/AlphaToMask-clip.png)
像素着色器(pixel Shader)内的clip()

通过在 pixel shader 中使用 `clip()` HLSL 指令，像素能被“丢弃”或不满足某些条件。这意味着对象仍然可以被认为是完全不透明的，并且没有绘制顺序问题。然而，这意味着所有像素是完全不透明或者透明的导致混叠*aliasing*（“锯齿”）。

通常，alpha-tested shader 也设置剪切(cutout)<u>渲染队列(render queue)</u>，所以 shader 代码看起来像这样：
	// 内部 SubShader
	Tags { "Queue"="AlphaTest" "RenderType"="TransparentCutout" "IgnoreProjector"="True" }

	// 内部 fragment Shader 的 CGPROGRAM:
	clip(textureColor.a - alphaCutoffValue);
#### Alpha-to-coverage
![](https://docs.unity3d.com/uploads/SL/AlphaToMask-4x.png)
AlphaToMask 开启，在 4x MSAA下

当使用多重采样抗锯齿(Multisample anti-aliasing, MSAA 参见 <u>QualitySettings</u>)，可以通过使用 alpha-to-coverage 的 GPU 功能改进 alpha testing 方法。这将改善边缘外观，质量取决于 MSAA 的使用等级。

此功能在大多数不透明或透明，并且具有非常薄的“部分透明”区域（类似草、叶子等）的 Texture 上最好地工作。

通常，alpha-to-coverage shader 也要设置剪切(cutout)<u>渲染队列(render queue)</u>。 所以着色器代码看起来像：
	// inside SubShader
	Tags { "Queue"="AlphaTest" "RenderType"="TransparentCutout" "IgnoreProjector"="True" }

	// inside Pass
	AlphaToMask On
### 示例
这个小例子可以添加一个纹理到屏幕上的任何东西：

	Shader "Simple Additive"
	{
    	Properties
		{
        	_MainTex ("Texture to blend", 2D) = "black" {}
		}
    	SubShader
		{
        	Tags { "Queue" = "Transparent" }
        	Pass
			{
            	Blend One One
            	SetTexture [_MainTex] { combine texture }
        	}
    	}
	}

## Pass: Pass Tags
Pass 使用 tag 来告知它们将期望何时及如何被渲染到渲染引擎
### 语法 *Syntax*
	Tags { "TagName1" = "Value1" "TagName2" = "Value"}
指定 **TagName1** 值为 **Value1**，**TagName2** 值为 **Value2**，Tag 可以有很多个。
### 说明
Tags 是基本的键值对。在<u>Pass</u> 内部的 tag 是用于控制哪一个任务应该在 Pass 光照管线*(lighting pipeline)*中（环境，顶点，像素光照等）和一些其他选项。请注意，以下标记必须在 Pass 部分中才能被 Unity 识别，而不是在SubShader中！
#### 光照模式标签 *LightMode tag*
光照模式*(LightMode)*定义了 Pass 在光照管线中的任务。更多细节请看<u>光照管线*(lighting pipeline)*</u>。这些标签很少手动使用，最常见的需要于光照交互的 shader 会被写成 <u>Surface Shader</u>，然后所有的细节都会被照顾到。

LightMode 可能的值有：

* **Always**：始终渲染；不应用光照。
* **ForwardBase**：使用<u>正向渲染*(Forward rendering)*</u>；环境，主平行光，顶点/SH光和光照贴图将被应用。
* **ForwardAdd**：使用<u>正向渲染*(Forward rendering)*</u>；叠加每个像素的光照，每个光都通过 Pass。
* **Deferred**：使用<u>延迟着色*(Deferred Shading)*</u>; 渲染 g-buffer。
* **ShadowCaster**：将对象深度渲染到阴影贴图或深度纹理中。
* **MotionVectors**：用来计算每个对象的运动矢量(motion vectors)。
* **PrepassBase**：使用传统的<u>延迟光照*(Deferred Lighting)*</u>，渲染法线和镜面指数(specular exponent)。
* **PrepassFinal**：使用传统<u>延迟光照*(Deferred Lighting)*</u>，通过组合纹理(combining texture)，光照和自发光渲染最终颜色。
* **Vertex**：当对象没有光照贴图(lightmap)时，使用<u>传统顶点光照*(Vertex Lit)*</u>渲染; 使用所有的顶点光照。
* **VertexLMRGBM**：当对象没有光照贴图(lightmap)时使用<u>传统顶点光照*(Vertex Lit)*</u>渲染; 在光照贴图是 RGBM 编码（PC和主机）的平台上。
* **VertexLM**：当对象没有光照贴图(lightmap)时使用<u>传统顶点光照*(Vertex Lit)*</u>渲染; 在光照贴图是 double-LDR 编码（移动平台）的平台上。

### PassFlags tag
Pass 可以指示改变<u>渲染管线*(rendering pipeline)*</u>如何向它传递数据。这是通过 PassFlags 标签完成的，它的值通过空格分隔。目前支持的 flag 有：

* **OnlyDirectional**：当在 ForwardBase Pass 类型中使用时，此 flag 使得只有主平行光和环境/光探测(lightprobe)数据被传递到 shader 中。这意味着非重要光的数据不会传递到顶点光照(vertex-light)或球面谐波(spherical harmonics) shader 变量。 有关详细信息，请参阅<u>前置渲染*Forward rendering*</u>。

### RequireOptions tag
Pass 可以指示它应当仅当满足一些外部条件时被渲染。这是通过 **RequireOptions** 标记完成的，它的值是一个空格分隔选项的字符串。目前 Unity 支持的选项有：

* **SoftVegetation**：仅当 <u>Quality Settings</u> 质量设置中的 Soft Vegetation 打开时才渲染此传递。

## Pass: 模板*Stencil*
模板缓冲器(stencil buffer)可以用作通用目的的每像素遮罩，用于保存或丢弃像素。
模板缓冲器通常是 8bit 整数/每个像素。该值可以写入，递增或递减。后续的绘制调用(draw call)可以测试该值，以决定是否应该在运行像素着色器之前丢弃一个像素。
### 语法 *Syntax*
#### Ref
	Ref referenceValue
将要比较的值（如果 Comp 是除了 *always* 以外的任何值）和/或要写入缓冲区的值（如果是 Pass，Fail 或 ZFail 其中一个且值为 *Replace*）。0-255 的整数。
#### ReadMask
	ReadMask readMask
用 0-255 整数表示的一个 8bit 的遮罩，在将引用值(reference value)与缓冲区的内容(contents of the buffer)进行比较时使用，*(referenceValue＆readMask)comparisonFunction(stencilBufferValue＆readMask)*。 默认值：*255*。
#### WriteMask
	WriteMask writeMask
用 0-255 整数表示的一个 8bit 的遮罩，写入缓冲区时使用。 默认值：*255*。
#### Comp
	Comp comparisonFunction
被用来进行比较引用值与缓冲区的当前内容的函数。 默认值：*always*。
#### Pass
	Pass stencilOperation
如果模板测试*stencil test*（和深度测试*depth test*）通过，如何处理缓冲区的内容。 默认值：*keep*。
#### Fail
	Fail stenciOperation
如果模板测试*(stencil test)*失败，如何处理缓冲区的内容。 默认值：*keep*。
#### ZFail
	ZFail stencilOperation
如果模板测试*(stencil test)*通过，但深度测试*depth test*失败，如何处理缓冲区的内容。 默认值：*keep*。

Comp，Pass，Fail 和 ZFail 将应用于正面(front-facing)几何体，除非指定 *Cull Front*，在这种情况下它是背面(back-facing)几何体。 您还可以显式指定双面模板状态（two-sided stencil state），通过这些定义： CompFront，PassFront，FailFront，ZFailFront（用于正面几何体）和 CompBack，PassBack，FailBack，ZFailBack（用于背面几何体）。
#### 比较函数*Comparison Function*
比较函数是以下其中之一：
|              |                                          |
| ------------ | ---------------------------------------- |
| **Greater**  | 仅渲染引用值大于缓冲区中值的像素。 |
| **GEqual**   | 仅渲染引用值大于或等于缓冲区中值的像素。 |
| **Less**     | 仅渲染引用值小于缓冲区中值的像素。 |
| **LEqual**   | 仅渲染引用值小于或等于缓冲区中值的像素。 |
| **Equal**    | 仅渲染引用值等于缓冲区中值的像素。 |
| **NotEqual** | 仅渲染引用值不等于缓冲区中值的像素。 |
| **Always**   | 让模板测试*stencil test*始终通过。 |
| **Never**    | 让模板测试*stencil test*始终失败。 |
#### 模板操作*Stencil Operation*
模板操作是以下其中之一：

|          |                                          |
| -------- | ---------------------------------------- |
| **Keep**     | 保持缓冲区的当前内容。 |
| **Zero**     | 把缓冲区写 0。 |
| **Replace**  | 把引用值写入缓冲区。 |
| **IncrSat**  | 增加缓冲区中的当前值。 如果值已经是255，它保持在255。 |
| **DecrSat**  | 减少缓冲区中的当前值。 如果值已经是0，它保持在0。 |
| **Invert**   | 反转所有 bits 位。 |
| **IncrWrap** | 增加缓冲区中的当前值。 如果值已经是255，它将变为0。 |
| **DecrWrap** | 减少缓冲区中的当前值。 如果值为0，则为255。 |
### 延迟渲染路径*Deferred rendering path*
在*延迟渲染路径(Deferred rendering path)*中渲染的对象的*模板功能(Stencil functionality)*有些受限，因为在基本传递(base pass)和光照传递(lighting pass)期间，模板缓冲区用于其他目的。在这两个阶段期间，在 shader 中定义的模板状态将被忽略，并且仅在最后一遍中被考虑。因为它不可能基于模板测试(stencil test)来遮盖(mask)这些对象，但它们仍然可以修改缓冲区内容，以供对象在之后的帧中渲染使用。在延迟路径之后(Deferred path)的正向渲染路径(forward rendering paht)中渲染的对象（例如透明对象或没有 surface shader 的对象）将再次被正常设置它们的模板状态。

延迟渲染路径(deferred rendering path)使用模板缓冲区的最高的3个 bit 位，最多可加到最高4个 bit位 - 这取决于场景中使用了多少光源遮罩层(light mask layers)。可以使用模板读写掩码(stencil read and write mask)在“干净” bit 位范围内操作，也可以在使用 **Camera.clearStencilAfterLightingPass** 在光照通过后强制相机清除模板缓冲区。

### Example
第一个示例 shader 将在任何深度测试通过的位置写入值“2”。 模板测试设置为始终通过。

	Shader "Red"
	{
    	SubShader
		{
        	Tags { "RenderType"="Opaque" "Queue"="Geometry"}
        	Pass
			{
            	Stencil
				{
                	Ref 2
                	Comp always
                	Pass replace
            	}
        
            	CGPROGRAM
            	#pragma vertex vert
            	#pragma fragment frag
            	struct appdata
				{
                	float4 vertex : POSITION;
            	};
            	struct v2f
				{
                	float4 pos : SV_POSITION;
            	};
            	v2f vert(appdata v)
				{
                	v2f o;
                	o.pos = UnityObjectToClipPos(v.vertex);
                	return o;
            	}
            	half4 frag(v2f i) : SV_Target
				{
                	return half4(1,0,0,1);
            	}
            	ENDCG
        	}
    	} 
	}

第二个 shader 将仅传递第一个（Red）shader 传递的像素，因为它检查了值与“2”是否相等。 它也将减少缓冲区中的值，无论 Ztest 是否失败。

	Shader "Green"
	{
    	SubShader
		{
        	Tags { "RenderType"="Opaque" "Queue"="Geometry+1"}
        	Pass
			{
    	        Stencil
				{
                	Ref 2
                	Comp equal
                	Pass keep 
                	ZFail decrWrap
            	}
        
            	CGPROGRAM
            	#pragma vertex vert
            	#pragma fragment frag
            	struct appdata
				{
                	float4 vertex : POSITION;
            	};
            	struct v2f
				{
                	float4 pos : SV_POSITION;
            	};
            	v2f vert(appdata v)
				{
                	v2f o;
                	o.pos = UnityObjectToClipPos(v.vertex);
                	return o;
            	}
            	half4 frag(v2f i) : SV_Target
				{
                	return half4(0,1,0,1);
            	}
            	ENDCG
        	}
    	} 
	}

第三个 shader 只会在模板值为“1”时传递，因此只有红色和绿色球体交叉处的像素 - 即模板被通过 Red shader 设置为“2”并被 Green shader 减少为“1”。

	Shader "Blue"
	{
    	SubShader
		{
        	Tags { "RenderType"="Opaque" "Queue"="Geometry+2"}
	        Pass
			{
           		Stencil
				{
                	Ref 1
                	Comp equal
            	}
        
            	CGPROGRAM
            	#include "UnityCG.cginc"
            	#pragma vertex vert
            	#pragma fragment frag
            	struct appdata
				{
                	float4 vertex : POSITION;
            	};
            	struct v2f
				{
                	float4 pos : SV_POSITION;
            	};
            	v2f vert(appdata v)
				{
                	v2f o;
                	o.pos = UnityObjectToClipPos(v.vertex);
                	return o;
            	}
            	half4 frag(v2f i) : SV_Target
				{
                	return half4(0,0,1,1);
            	}
            	ENDCG
        	}
    	}
	}

结果如下：
![](https://docs.unity3d.com/uploads/Main/StencilExample.png)

另一个例子的效果更直接。 首先使用此 shader 在模板缓冲区中标记正确的区域内渲染球体：

	Shader "HolePrepare"
	{
    	SubShader
		{
        	Tags { "RenderType"="Opaque" "Queue"="Geometry+1"}
        	ColorMask 0
        	ZWrite off
       		Stencil
			{
            	Ref 1
            	Comp always
            	Pass replace
        	}

        	CGINCLUDE
            struct appdata
			{
                float4 vertex : POSITION;
            };
            struct v2f
			{
                float4 pos : SV_POSITION;
            };
            v2f vert(appdata v)
			{
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                return o;
            }
            half4 frag(v2f i) : SV_Target
			{
                return half4(1,1,0,1);
            }
        	ENDCG

        	Pass
			{
            	Cull Front
            	ZTest Less
        
            	CGPROGRAM
            	#pragma vertex vert
            	#pragma fragment frag
            	ENDCG
        	}
        	Pass
			{
            	Cull Back
            	ZTest Greater
        
            	CGPROGRAM
            	#pragma vertex vert
            	#pragma fragment frag
            	ENDCG
        	}
    	} 
	}

然后再次渲染为一个相当标准的 surface shader，除了前面剔除(front face culling)，禁用深度测试并让模板测试丢弃以前标记的像素：

	Shader "Hole"
	{
    	Properties
		{
	        _Color ("Main Color", Color) = (1,1,1,0)
	    }
    	SubShader
		{
        	Tags { "RenderType"="Opaque" "Queue"="Geometry+2"}

        	ColorMask RGB
	        Cull Front
        	ZTest Always
        	Stencil
			{
            	Ref 1
            	Comp notequal 
        	}

        	CGPROGRAM
        	#pragma surface surf Lambert
        	float4 _Color;
        	struct Input
			{
            	float4 color : COLOR;
        	};
        	void surf (Input IN, inout SurfaceOutput o)
			{
            	o.Albedo = _Color.rgb;
           		o.Normal = half3(0,0,-1);
            	o.Alpha = 1;
        	}
        	ENDCG
    	} 
	}

结果如下：
![](https://docs.unity3d.com/uploads/Main/StencilExample2.png)

## Pass: Name
### 语法 *Syntax*
	Name "PassName"
指定 *Pass Name* 为当前 Pass 的名字。注意，它内部的名称会都被转换成大写。
### 说明
Pass 可以被指定名词以便于 <u>UsePass</u> 指令能够引用它。

## Pass: Legacy Lighting
Material 和光照参数(lighting parameters)被用于控制内置顶点光照。 顶点光照是为每个顶点计算的标准 Direct3D/OpenGL 光照模型。 **Lighting on** 是开启光照。 光照会受到 **Material** 块，**ColorMaterial** 和 **SeparateSpecular** 指令的影响。

*注意：当使用<u>顶点程序vertex programs</u>时，Material/Lighting 指令没有效果; 因为在这种情况下所有的计算都完全在 shader 中描述。 建议使用可编程 shader，而不是传统的顶点光照。 这样的话，你将不会使用这里描述的任何命令，而是自己定义你的 <u>vertex and fragment programs</u> ，并在那里处理所有的光照，纹理和其他任何东西。*

### 语法 *Syntax*

顶层命令(top level commands)控制是否使用固定功能光照，以及一些配置选项。 主要设置在 **Material Block** 中，下面进一步详细说明。

#### Color
	Color color
将对象设置为纯色。 颜色为括号中的四个 RGBA 值，或方括号中的颜色名。
#### Material
	Material { Material Block }
Material Block 用于定义对象的 Material 资产。
#### Lighting
	Lighting On | Off
如果要对 Material Block 中定义的设置具有任何效果，必须使用 *Lighting On* 命令启用光照。 如果光照关闭，则颜色直接从 `Color` 命令中取得。
#### SeparateSpecular
	SeparateSpecular On | Off
此命令使反射光照(specular lighting)添加到 shader pass 的末尾，因此反射光照不受纹理的影响。 仅在使用 *Lighting On* 时有效。
#### ColorMaterial
	ColorMaterial AmbientAndDiffuse | Emission
使用每顶点颜色(per-vertex color)而不是材质中设置的颜色。 **AmbientAndDiffuse** 替换材质的环境(Ambient)和漫反射(Diffuse)值; **Emission** 替代材质的自发光值。
#### Material Block
这里面包含了材料对光如何反应的设置。任何这些属性都可以省略，在这种情况下，它默认为黑色（即没有效果）。

**Diffuse color**：漫反射颜色组件。这是一个对象的基本颜色。

**Ambient color**：环境颜色组件。这是当 <u>Lighting Window<> 中设置的环境光照射时对象所具有的颜色。

**Specular color**：对象反射高光的颜色。

**Shininess number**：亮度的锐度，值在0和1之间。当为0时你会得到一个巨大的亮点，看起来很像漫射照明；当为1你会看到一个微小的斑点。

**Emission color**：物体没有被任何光照射时的颜色。

被光源照射对象的所有颜色是：

**Ambilent** \* <u>Lighting Window的环境亮度设置*Ambient Intensity setting*</u> + (Light Color \* **Diffuse** + Light \* Specular) + Emission

对于击中对象的所有光，重复方程的光部分（在括号内）。
???????
通常，您会希望 Diffuse 和 Ambient 保持的颜色相同（所有内置的 Unity 着色器都会这样做）。

## Pass: Legacy Texture Combiners
???????
## Pass: Ldegacy Alpha Testing
???????
## Pass: Legacy Fog
雾参数由 Fog 命令控制。

雾化(Fogging)是基于与相机的距离,将生成的像素的颜色向下混合而成的恒定颜色。雾化不会修改混合像素的Alpha值，只会修改其RGB分量。
### 语法 *Syntax*
#### Fog
	Fog { Fog COmmand}
在花括号中指定 Fog 指令
#### Mode
	Mode Off | Flobal | Linear | Exp | Exp2
定义雾模式。默认值为全局，根据 Render Settings 来设置是否启用雾，该值可转换为 Off 或 Exp2。
#### Color
	Color ColorValue
设置雾颜色。
#### Density
	Density FloatValue
设置指数雾的密度
#### Range
	Range FloatValue, FloatValue
设置近距离和远距离的线性雾。
## 说明
默认雾设置基于 Lighting Window 中：雾模式为 Exp2 或 Off ; 密度和颜色。
请注意，如果您使用片段程序(fragment programs)，则仍将应用 shader 的雾设置。 在没有固定功能(fixed function)的平台上，Unity 将在运行时修补 shader 以支持请求的雾模式。

## Pass: Legacy BindChannels
**BindChannels** 命令允许您指定顶点数据如何映射到图形硬件。

*注意：当使用<u>顶点程序(vertex programs)</u>时，_BindChannels 是没有效果的。因为绑定(bindings)由 vertex shader 输入控制。 建议使用可编程 shader ，而不是固定功能顶点处理(fixed function vertex processing)。*

默认情况下，Unity 会找出你的绑定，但在某些情况下，你会需要使用自定义绑定。

例如，您可以映射要在第一个纹理阶段中使用的主要 UV 和要在第二个纹理阶段中使用的辅助 UV ; 或告知硬件顶点颜色需要考虑在内。

### 语法 *Syntax*
	BindChannels { Bind "source", target }
指定顶点数据 source 映射到硬件 target。

**Source** 可以是以下其中一个：

* **Vertex**: 顶点坐标
* **Normal**: 顶点法线
* **Tangent**: 顶点切线
* **Texcoord**: 主 UV 坐标
* **Texcoord1**: 次 UV 坐标
* **Color**: 每个顶点颜色

**Target** 可以是以下其中一个:

* **Vertex**: 顶点坐标
* **Normal**: 顶点法线
* **Tangent**: 顶点切线
* **Texcoord0, Texcoord1, …**: 每个纹理阶段对应的纹理坐标
* **Texcoord**: 所有纹理阶段的纹理坐标
* **Color**: 顶点颜色

### 说明
Unity 对哪些 source 可以映射到哪些 target 有一些限制。 source 必须和 target **Vertex**，**Normal**，**Tanget**和 **Color** 匹配。 来自网格（**Texcoord** 和 **Texcoord1**）的纹理坐标可以映射到纹理坐标 target（所有纹理阶段的 **Texcoord**，或特定阶段的**TexcoordN**）。

BindChannels 有两个典型的用例：

* 需要考虑顶点颜色的 shader。
* 使用两个 UV 集的 shader。

### 示例

	//将第一个 UV 集映射到第一个纹理阶段
	//第二 UV 集合映射到第二纹理阶段
	BindChannels
	{
		Bind "Vertex", vertex
		Bind "texcoord", texcoord0
		Bind "texcoord1", texcoord1
	}



	//将第一个 UV 集映射到所有纹理阶段
	//并且使用顶点颜色
	BindChannels
	{
		Bind "Vertex", vertex
		Bind "texcoord", texcoord
		Bind "Color", color
	}

# SubShader: UsePass
用 UsePass 指令使用来自另一个 shader 的 pass 。
## 语法 *Syntax*
	UsePass "Shader/Name"
从指定的 shader 插入指定名称的所有 pass 。*Shader/Name* 包含 shader 和 pass 的名字，用斜杠分割，注意，只有第一个支持的 <u>subshader</u> 会被顾及到。
## 说明
一些 shader 能够重用来自于其他 shader 的已有 pass ，从而减少代码重复。例如，你可能有一个绘制对象外轮廓(outline)的 shader pass，并且你想在其他 shader 中重用这个 pass。 UsePass 指令就能做到：引入来自另一个 shader 中指定的 pass。例如，下面的指令就是使用来自内置 shader VertexLit 的 "SHADOWCASTER" pass。
	UsePass "VertexLit/SHADOWCASTER"
为了使 UsePass 工作，必须给定一个希望使用的 pass 的名字。在 pass 中用 <u>Name</u> 指令指定。
	Name "MyPassName"

# SubShader: GrabPass
GrabPass 是一种特殊的 pass 类型，他抓取屏幕的内容，其中的对象将被绘制到纹理中。这种纹理可以在后续的 pass 中使用，用来做基于图像的高级效果。
## 语法 *Syntax*
GrabPass 属于 <u>subshader</u> 内。 它可以采取两种形式：
* 只使用 `GrabPass { }` 抓取当前屏幕内容到纹理。纹理可以在之后的 Pass 里通过 _GrabTexture 访问。注意：这种形式的 GrabPass 将对每个使用它的对象执行耗时的屏幕抓取操作。
* `GrabPass { "TextureName" }` 会将当前屏幕内容到纹理中，但是对于给定纹理名称的第一个对象，只会每帧执行一次。纹理可以在后续的 Pass 种使用给定纹理名称访问。对于在场景中多个 object 使用 GrabPass 时这是一个高效的方法。
  此外， GrabPass 也可以使用 <u>Name</u> 和 <u>Tag</u> 指令。
## 示例
这是一种低效的反转之前渲染颜色的方法：

	Shader "GrabPassInvert"
	{
		SubShader
		{
			// 在所有的不透明图形绘制后再绘制自己
			Tags { "Queue" = "Transparent" }
	
			// 抓取对象后面的屏幕到 _BackgroundTexture
			GrabPass
			{
				"_BackgroundTexture"
			}
	
			// 使用上面生成的纹理渲染对象，并反转颜色
			Pass
			{
				CGPROGRAM
				#pragma vertex vert
				#pragma fragment frag
				#include "UnityCG.cginc"
				struct v2f
				{
					float4 grabPos : TEXCOORD0;
					float4 pos : SV_POSITION;
				};
				v2f vert(appdata_base v)
				{
					v2f o;
					// 使用 UnityCG.cginc 中的 UnityObjectToClipPos 计算 
					// 裁剪空间的顶点
					o.pos = UnityObjectToClipPos(v.vertex);
					// 使用 UnityCG.cginc 中的 ComputeGrabScreenPos 
					// 取得正确的纹理坐标
					o.grabPos = ComputeGrabScreenPos(o.pos);
					return o;
				}
				sampler2D _BackgroundTexture;
				half4 frag(v2f i) : SV_Target
				{
					half4 bgcolor = tex2Dproj(_BackgroundTexture, i.grabPos);
					return 1 - bgcolor;
				}
				ENDCG
			}
		}
	}


这个着色器有两个 Pass ：第一个 Pass 在渲染时抓取对象后面的任何对象，然后在第二个 Pass 中应用它。 注意，使用<u>反转混合模式(blend mode)</u>可以更有效地实现相同的效果。

# SubShader: SubShader Tags
SubShader 使用 Tag 来说明它们将如何以及希望何时将其渲染到渲染引擎。
## 语法 *Syntax*
`Tags { "TagName1" = "Value1" "TagName2" = "Value2" }`

指定 TagName1 值为 Value1，TagName2 值为 Value2。您可以按照需要写更多的 Tags 。

## 说明

Tags 的本质是键值对。在 <u>SubShader</u> 内，Tags 用于确定渲染顺序和 subshader 的其他参数。需要注意：以下 Unity 能够识别的 Tags 必须写在 SubShader 内，而不是 Pass 内！

除了由 Unity 识别的内置标签，您可以使用自己的标签，并使用 <u>Material.GetTag</u> 函数查询它们。

### 渲染顺序 -  Oueue 标签

你能使用 *Queue* 标签来决定你的 object 绘制顺序。Shader 决定它的 object 属于哪个渲染队列，这样确保任何 Transparent shader 在所有 Opaque 对象之后绘制等等。

一共有四个预定义的渲染队列，但在预定义的渲染队列之间可以有更多的队列。预定义的队列有：

* `Background` - 此渲染队列在任何其他渲染之前。典型的情况是用在那些真正需要成为背景的东西上。

* `Geometry`(默认) - 用于大多数对象，不透明几何使用此队列。

* `AlphaTest` - Alpha测试的几何图形使用此队列，它是分割自 `Geometry` 的独立队列。因为在绘制所有实体后，渲染经过 alpha 测试的对象效率更高。

* `Transparent` - 此渲染队列在 Geometry 和 `AlphaTest` 之后，以从前到后的顺序呈现。任何 alpha 混合（即不写入深度缓冲区(depth buffer)的 shader ）都应该在这里（玻璃，粒子效果）。

* `Overlay` - 此渲染队列用于叠加效果。任何最后呈现的东西都应该这这里（例如镜头光晕）。

  	Shader "Transparent Queue Example"
  	{
  		SubShader
  		{
  			Tags { "Queue" = "Transparent" }
  			Pass
  			{
  				// shader 的其余部分...
  			}
  		}
  	}
*一个说明如何在透明队列中渲染某物的示例*

对于特殊用途，可以使用中间队列。在内部实现里每个队列都由整数索引表示；Background 是 1000，Geometry 是 2000， AlphaTest 是 2450，Transparent 是 3000， Overlay 是 4000。如果 shader 使用这样的队列：

`Tags { "Queue" = "Geometry+1" }`

这将使对象渲染在所有 Opaque 对象后，但在 Transparent 对象之前，作为渲染队列索引将是2001( Geometry 加一)。当您希望某些对象始终在其他对象集之间绘制的情况下非常有用。例如，在大多数情况下，透明的水应该在不透明物体之后但在透明物体之前绘制。

队列超过2500(Geometry + 500)被认为“不透明”，并优化对象的绘制顺序以获得最佳性能。对于“透明对象”考虑较高的渲染队列，并按距离对对象进行排序，从最远的一个开始渲染，并以最近的一个结束。 Skybox 在所有不透明和所有透明对象之间绘制。

### RenderType 标签

`RenderType` 标签把 shader 归类到预定义的组，例如 opaque shader 或 alpha-test shader 等。 这将被<u>着色器替换Shader Replacement</u>使用并在某些情况下使用<u>摄像机深度纹理camera’s depth texture</u>。

### DisableBatching 标签

使用时 shader（主要是那些进行对象-空间顶点变换）在 <u>Draw Call Batching</u> 时不会工作，这是因为批处理将所有几何图形变换到世界空间中，所以对象空间会丢失。

`DisableBatching` 标签可以用来消除。它可能有三个值：“True”（始终禁用此 shader 的 batching），“False”（不禁用 batching; 默认值）和 “LODFading”（当 LOD 活动衰减时禁用 batching; 主要用于树）。

### ForceNoShadowCasting 标签

如果标记了 `ForceNoShadowCasting` 且值为 True ，则使用此 subshader 渲染的对象将永远不会投射阴影。当你在透明对象上使用着色器替换，并且不继承来自另一个 subshader 的阴影 pass 时，这是最有用的。

### IgnoreProjector 标签

如果标记了 `IgnoreProjector` 且值为 True，则使用此 shader 的对象不会受到<u>透视投影(Projectors)</u>的影响。这对半透明对象最有用，因为没有好的方法让透视投影(Projectors)影响它们。

### CanUseSpriteAtlas 标签

如果着色器用于 sprite 且将 `CanUseSpriteAtlas` 标记设置为 False ，当它们打包到 atlases 中时不会工作（请参阅<u>Sprite Packer</u>）。

### PreviewType标签

`PreviewType` 指示 Material Inspector 预览应如何显示材质。默认情况下，材质显示为球体，但PreviewType也可以设置为“Plane”（显示为2D平面）或“Skybox”（将显示为天空盒）。

# ShaderLab Syntax: Fallback

在所有的 subshader 之后可定义一个 Fallback 。它基本的意思是“如果没有一个 subshader 能够在这个硬件上运行，尝试使用这一个 shader ”。

## 语法 *Syntax*

	Fallback "name"
Fallback 可以是一个指定名称的 shader 或者

	Fallback Off
明确声明没有 Fallback 并且不打印警告信息，即便是在没有 subshader 能在此设备上运行的情况下。

## 说明

Fallback 声明具有来自其他 shader 的所有 subshader 被插入到其位置相同的效果。

## 示例
	Shader "example"
	{
		// properties 和 subshader
		Fallback "otherexample"
	}

# ShaderLab Syntax: CustomEditor

你可以为你的 shader 定义 CustomEditor。Unity 将会寻找这个名字的类来拓展 ShaderGUI。如果任何 Material 使用了这个 shader 就将使用这个 ShaderGUI。示例参见 <u>Custom Shader GUI</u> 。

## 语法 *Syntax*

	CustomEditor "name"
使用指定 *name* 的 ShaderGUI 。

## 说明

CustomEditor 的效果将出现在所有用到这个 shader 的 Material 上。

## 示例

	Shader "example"
	{
		// properties 和 subshader
		CustomEditor "MyCustomEditor"
	}

# ShaderLab Syntax: 其他指令

## Category

Category 是其下任何命令的逻辑分组。它主要用于“继承”渲染状态。例如，你的 shader 可能有多个 subshader ，每一个都需要关闭雾气(fog)，混合模式(blending)为叠加(additive)等。你可以像这样使用 Category ：

	Shader "example"
	{
		Category
		{
			Fog { Mode Off }
			Blend One One
			SubShader
			{
				//... 
			}
			SubShader
			{
				//...
			}
			// ...
		}
	}

Category 块只在 shader 解析(parsing)时候有效，它与把 Category 的内容"粘贴(pasting)"到各个块中是完全相同的。不影响 shader 执行速度。

# Shader assets
<u>阅读 Shader Scripting API</u>
Shader 是包含了供图形卡执行的代码和指令的资源。<u>Materials</u> 引用了 shader，并且设置它们的参数（texture，颜色等）。

Unity 包含了一些内置 shader,它们在项目中始终可用（例如，<u>标准着色器*Standard shader*</u>）。<u>Image Effects</u> package 也有很多用于图像后期处理的 shader。你也可以看 <u>write your own shader</u>。
## 创建一个新 shader
要创建新的着色器，请从主菜单或 **Project View** 上下文菜单中使用 **Assets** > **Create** > **Shader**。Shader 是一个类似于 C# 脚本的文本文件，并且是以 Cg/HLSL 和 ShaderLab 语言的组合编写的（有关详细信息，请参阅 <u>writing shaders</u> 页面）。
![](https://docs.unity3d.com/uploads/Shaders/Inspector-Shader.png)
Shader Inspector 面板
### Shader 导入设置
Inspector 部分允许指定 shader 的默认 texture。每当使用此 shader 创建新 Material 时，将自动分配这个 texture。
### Shader Inspector
Shader Inspector 显示关于 shader（主要是<u>shader tag</u>）的基本信息，并允许编译和检查低级编译代码。

对于 Surface Shader，“显示生成的代码(Show generated code)”按钮能显示 Unity 生成的用于处理光照和阴影的所有代码。如果你真的想自定义生成的代码，你可以复制和粘贴所有内容的到你的原始 shader 文件，并开始调整。
![](https://docs.unity3d.com/uploads/Shaders/Inspector-ShaderCompilePopup.png)
Shader 编译弹出菜单

**编译和显示代码按钮(Compile and show code)**的弹出菜单允许为选定的平台检查最终编译的 shader 代码（例如，在 Direct3D9 上的汇编，或者针对 OpenGL ES 的低层次(low-level)优化的 GLSL）。 这在优化 shader 的性能时最有用; 通常你会想知道最后生成了多少个低级指令(low-level instructions)。

低级指令生成的代码可用于粘贴到GPU着色器性能分析工具（如 <u>AMD GPU ShaderAnalyzer</u> 或 <u>PVRShaderEditor</u>）中。

## Shader 编译细节
在导入 Shader 的时候，Unity 不会编译整个 shader。这是因为大多数 shader 中都有很多<u>变体(variants)</u>，并且对所有可能的平台进行编译都需要很长时间。相反，它真正做的是：

* 在导入时，只对 shader 进行最少的处理（surface shader生成等）。
* 仅在需要时才实际编译着色器变量。
* 代替在导入时编译100-10000个内部 shader 的标准工作，通常最后只有少量会被编译。

在播放器构建(player build)时，所有“尚未编译”的着色器变体都会被编译。所以即使编辑器没有使用它们，它们也会在游戏数据中。
但是，这意味着可能在某一个 shader 导入时没有检测到其中有错误。例如，你使用 Direct3D 11 运行 editor，但如果是为 OpenGL 编译，shader 则会出错，或者着色器的某些变体不适合着色器模型(Shader Model)2.0指令限制等。如果 editor 需要它们这些错误将显示在 Inspector 中；但这也是一个好的做法，手动完全编译你所需平台的 shader，以检查错误。还可以使用在 Shader Inspector 中的 **Compile and show code** 弹出菜单来完成。

每当需要 Unity 需要编译 Shader 时，Unity 会启动名为 `UnityShaderCompiler` 的后台进程进行编译。多个编译器进程能被启动（通常是每个 CPU 核心一个），以便在播放器构建时能够并行完成 shader 编译。虽然 editor 不编译 shader，编译器进程什么也不做并且不消耗计算机资源，所以没有必要当心它们。当 Unity editor 退出时，它们也将关闭。

单个 shader 变体编译结果缓存在项目中的 `Library/ShaderCache` 文件夹下。这意味着100％相同的 shader 或其代码段将重用以前编译的结果。这也意味着如果你有很多着色器经常要更改，shader 缓存文件夹可能变得相当大。删除它们始终是安全的，删除只会让着色器变体被重新编译。

# 高级 ShaderLab 主题
阅读并提升你的 ShaderLab 功夫！
???????

## 替换 Shader 渲染 *(Rendering with Replaced Shaders)*

一些渲染效果需要使用不同的着色器集渲染场景。例如，良好的边缘检测将需要具有场景法线的纹理，因此其可以检测表面取向不同的边缘。 其他效果可能需要具有场景深度的纹理等等。 为了实现这一点，可以用所有对象的替换着色器来渲染场景。

着色器替换*(Shader replacement)*是使用脚本的函数 <u>Camera.RenderWithShader</u> 或 <u>Camera.SetReplacementShader</u> 来完成的。 这两个函数都使用 **shader** 和 **replacementTag** 。

它的工作原理是：camera 按照通常的情况来渲染场景。object 仍然使用它们的 materials ，但是最终会被它们实际使用的shader改变：

* 如果 **replacementTag**  是空的，则使用给定的替换着色器(replacement shader)渲染场景中的所有对象。
* 如果 **replacementTag** 不为空，那么对于将要渲染的每个对象：
  * 将查询真正 object 的shader的 <u>tag 值</u>。
  * 如果没有tag，object将**不被渲染**。
  * 在替换着色器(replacement shader)中找到一个 <u>subshader</u> ，该 subshader 具有根据 tag 找到的值。如果没有找到这样的subshader，则不渲染对象。
  * 现在，将 subshader 用于渲染对象。

因此，如果所有着色器都有例如“RenderType”标记，其值为“Opaque”，“Transparent”，“Background”，“Overlay”，则可以写一个替换着色器，只使用一个 RenderType = Solid 标记的 subshader。因为在替换着色器中将找不到其他标记类型，所以该对象不会被渲染。 或者你可以为不同的“RenderType”标签值写几个subshaders。 顺便说一句，所有内置的Unity着色器都有一个“RenderType”标签集。

## Lit Shader 替换

当使用着色器替换时，会使用 camera 上配置的渲染路径来渲染场景。这意味着用于替换的 shader 可以包含阴影和光照通道（您可以使用 surface shader 来进行着色器替换）。这对于渲染特殊效果和场景调试很有用。

##内置 Unity shader 中着色器替换 *(Shader replacement)* 标签

所有内置的Unity着色器都有一个“RenderType”标签集，可以在使用替换的着色器进行渲染时使用。标记值如下：

* Opaque*(不透明)*：大多数着色器（<u>Normal*(正常)*</u>，<u>Self Illuminated*(自发光)*</u>，<u>Reflective*(反光)*</u>，地形着色器）。

* Transparent*(透明)*：大多数半透明着色器（<u>Transparent</u>，粒子，字体，地形添加通过着色器）。

* TransparentCutout：隐藏的透明度着色器（<u>Transparent Cutout</u>，两遍植被着色器）。

* Background：Skybox着色器。

* Overlay：GUITexture，Halo，Flare着色器。

* TreeOpaque：地形树皮。

* TreeTransparentCutout：地形树叶。

* TreeBillboard：地形 Billboard 树。

* Grass：地形草。

* GrassBillboard：地形 Billborad 草。


## 内置场景深度(depth)/法线(normals)纹理

如果你的效果需要的话，camera 具有内置的能力来渲染深度(depth)或深度(depth)+法线(normals)纹理。 请参阅 <u>Camera Depth Texture</u> 页面。 请注意，在某些情况下（取决于硬件），深度和深度+法线纹理可以使用着色器替换在内部渲染。 因此，在 shader 中使用正确的 “RenderType” 标记很重要。

## 代码示例

你的 Start() 函数里指定替换着色器：

	void Start()
	{
	  camera.SetReplacementShader(EffectShader, "RenderType");
	}

这要求 EffectShader 使用 RenderType key。EffectShader 会为每个你想要的 RenderType 设置键值对标签。

shader 像这样：

	Shader "EffectShader"
	{
		SubShader
		{
			Tags { "RenderType"="Opaque" }
			Pass { ... }
		}
		SubShader
		{
			Tags { "RenderType"="SomethingElse" }
			Pass { ... }
		}
		...
	}

SetReplacementShader 将遍历场景中的所有对象，并使用特定键匹配到的值替代它们正常使用的 shader 。在这个例子中，任何具有 Rendertype = "Opaque" 标签 shader 的对象将被 EffectShader 中的第一个 subshader 替换，任何具有 Rendertype = "SomethingElse" 标签 shader 的对象将被 EffectShader 中的第二个 subshader 替换，等等。任何在 shader 中没有特殊键匹配到值的标签的对象将不会被渲染。

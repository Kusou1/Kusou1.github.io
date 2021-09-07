---
layout:       post
title:        "图形学概念导论"
date:         2021-08-01 11:31:00
author:       "zhangshaohong"
header-img:   "img/post-bg-2015.jpg"
catalog:      true
mathjax:      true
tags:
    - Graphics
    - Math
---

> 光线追踪器「raytracer」和光栅化器「rasterizer」采用不同的方法将3d场景渲染到2d屏幕上。然而，这两种方法都有一些共同的基本概念。这篇文章将在canvas上的像素「pixels」来定义坐标系「coordinate system」,以及如何表示和操作颜色.

## The Canvas

在接下来几个章节里，我们将在Canvas上绘制东西: 一个矩形的像素数组，可以单独着色。我们将是在一个2D的屏幕上表示一个3D场景,因此我们首先要知道渲染这个抽象的矩形像素数组。

我们将用一个非常简单点的function来完成这个任务💡,为画布上的一个像素分配一种颜色:

```
canvas.PutPixel(x, y, color)
```


这个方法有三个参数: $x$ 坐标、 $y$ 坐标和颜色。现在让我们把注意力集中在坐标「coordinates」上。

### 坐标系统「Coordinate Systems」


canvas有一个width和一个height(以pixels为单位) ，我们称之为 $C_w$ 和 $C_h$。我们需要一个坐标系来指代它的像素。对于大多数计算机屏幕，原点位于左上角; x 向屏幕右边增加，y 向底部增加，如图1-1所示。
![Figure 1-1: The coordinate system used by most computer screens](https://gabrielgambetta.com/computer-graphics-from-scratch/images/00-coordinates1.png)

<center>图1-1: 大多数计算机屏幕使用的坐标系</center>


对计算机来说，这种坐标系是自然,但对于人类并不是。3d图形程序员倾向于使用通常用于在纸上绘制图形的坐标系: 原点在中间，x向右增加，向左减少，y向上增加，向下减少，如图1-2所示。

![Figure 1-2: The coordinate system we’ll use for our canvas](https://gabrielgambetta.com/computer-graphics-from-scratch/images/00-coordinates2.png)
<center>图1-2: 我们在Canvas上使用的坐标系</center>

x坐标的范围是 $ [{-C_w \over 2},{C_w \over 2}) $
y 坐标的范围是$ [{-C_h \over 2}, {C_h \over 2}) $
. 我们只让 `PutPixel` 在这个范围内有效.

Canvas将绘制在屏幕上，因此我们需要从一个坐标系转换到另一个。要做到这一点，我们需要改变坐标系的中心和 y 轴的相反方向。由此得到的换算公式如下:
\$$ S_x = {C_w \over 2} + C_x $$

以及
\$$ S_y = {C_h \over 2} - C_y $$



我们定义 `PutPixel` 自动完成这个转换, 这之后，我们可以认为画布的坐标原点在中心，x向右增加,y向屏幕顶部增加。

让我们看看 `PutPixel` 的其余参数: the color.

## 颜色模型「Color Models」

我们看东西其实就是用眼睛接收可见光。可见光是电磁波的一个频段。我们把感受到的不同频率的电磁波映射成不同的颜色。遇到过高或过低频率的电磁波（红外线等等）眼睛处理不了，所以就看不见。

所以，**颜色只是人的主观感受，不是物体的客观属性**。物体只是在发射或反射电磁波。

波长和频率是反相关的: 波击中的频率越高，波峰之间的距离就越小，波携带的能量就越多。这就是为什么红外线(波长超过740纳米，对应低于405太赫兹的频率)是无害的，但是紫外线(波长短于380纳米，对应高于790太赫兹的频率)可以灼伤你的皮肤。

### 人眼看到的颜色与可见光谱上的频率是怎样对应的？

色彩有三个方面，色相，亮度，饱和度。色相就是颜色类别，比如红色和黄色。色相实质上就是电磁波的频率。可见光谱上表示的都是色相的变化。如图2-1所示

<img src="https://pica.zhimg.com/50/f121ecb89855c728a84bddce65de396f_720w.jpg?source=1940ef5c" data-rawwidth="600" data-rawheight="320" class="origin_image zh-lightbox-thumb" width="600" data-original="https://pic1.zhimg.com/f121ecb89855c728a84bddce65de396f_r.jpg?source=1940ef5c"/>
<center>图2-1: 可见光谱</center>

亮度顾名思义指颜色的亮度，本质上就是电磁波的幅度。电磁波幅度大，亮度就高，这个颜色就亮。

饱和度又称纯度，是指颜色中白光的比例。饱和度高的话，白光就少，颜色就比较正，比如大红色。饱和度低的话白光比例大，颜色比较发白。

所以说**光谱只是表示了色相这一维度**。不能完全表示我们见到的颜色。我们见到的颜色是三维构成的。

<img src="https://pica.zhimg.com/50/53cf3948718d762e70dd854d2640afbc_720w.jpg?source=1940ef5c" data-rawwidth="220" data-rawheight="176" class="content_image" width="220"/>

<center>图2-2: HSV颜色空间</center>

如上图2-2所示，H那圈代表hue（色相），S方向代表saturation（饱和度），V代表value（亮度）。另外，生活中的颜色一般都不是上图的那么纯，因为都混有杂色，这使得我们感受到的“色彩”更加丰富。PS：我记得前几年还流行一个说法，粉色不存在，是人脑虚构的。这种说法大概由于没在光谱上找到粉色吧。上图我们可以看见粉色部分，粉色其实就是饱和度低的红色。

### 混和颜色可以代替单一颜色（频率的光）吗？

如上所述，不同的色相对应着不同的频率，而任何两个频率相加是无法合成另一个频率的。所以说红光和绿光混合，得到包含两种频率的“黄光”只是人眼的感觉，和单一频率的黄光本质不同，只是人眼无法区分。接下来解释为什么说色光三原色（红绿蓝）可以混合出所有的“颜色”。这就涉及到人眼对颜色的感知了。视网膜上有两种细胞，杆状细胞能感受光的刺激产生明亮感觉，锥状细胞不仅能产生明亮感觉还能辨别颜色。根据视觉三色学说，锥状细胞有三种，分别对红绿蓝三种光线的刺激最敏感。下图2-3，横轴是光的波长，纵轴是三种锥状细胞对所有颜色的感应强度。
<img src="https://pic3.zhimg.com/50/d9a01e5710a7cd890ddb5ec688520a5d_720w.jpg?source=1940ef5c" data-rawwidth="709" data-rawheight="546" class="origin_image zh-lightbox-thumb" width="709" data-original="https://pic2.zhimg.com/d9a01e5710a7cd890ddb5ec688520a5d_r.jpg?source=1940ef5c"/>
<center>图2-3: 人视网膜中三种不同视锥细胞的光谱相对敏感性</center>


比如说，眼睛接收到红光，那么蓝色锥状细胞没感觉，绿色锥状细胞基本没感觉，红色锥状细胞感觉很强烈，那么大脑认为 ——这是红光咯。又来一个纯黄光（单一频率），红色和绿色锥状细胞都有一定的感觉，但是不太强烈，那么大脑觉得，这是黄光。再然后，红光和绿光合起来射向眼睛，这次感觉和上次一样，还是红色和绿色锥状细胞都有一定感觉，那么大脑依然觉得这是黄光。所以说，混和频率光在**人的感觉上**是可以代替单一频率的光的。这只是人的感受。实质上多个频率的光相加是不能合成另一个频率的光的。

### 不同的生物眼中的同一颜色（频率的光）是否一样呢？如果他们都不是色盲。

不同的生物对同一频率光的感受肯定不同。夜视动物（老鼠等）基本没有锥状细胞，那么他们无法分辨色相（光的频率），只能分辨亮度（光的强度），所以看到的是黑白世界。又比如，据说螳螂虾有12种锥状细胞，那么它们大概就可以区分红绿合成的“黄色”和单一频率的黄色。它们可以看见更丰富的颜色。这个真是难以想象啊。


每一种可以想象的颜色都可以用不同波长的组合来描述.例如，“White”是所有波长的总和,而“black”是指没有任何光。通过列出组成颜色的所有波长来描述颜色是不切实际的; 幸运的是，几乎所有的颜色都可以描述为只有3种颜色的线性组合，我们称之为原色「primary colors」。

### 减色法模型「Subtractive Color Model」
减色法模型是有趣的，它就像我们小时候用蜡笔在白纸上做的事情。给你一张白纸和红、蓝、黄色的蜡笔。当你在白纸上画一个黄色的圆圈，和一个蓝色的圆圈，当它们重叠时你得到绿色！黄色和红色得到橙色！红色和蓝色得到紫色！把这三样东西混合在一起——那你将得到黑色！Wasn’t kindergarten amazing? 

图2-4显示了减色模型的原色，以及混合后的颜色。

![Figure 1-3: Subtractive primary colors and their combinations](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries1.png)
<center>图2-4: 减色法原色及其组合</center>

物体有不同的颜色，因为它们以不同的方式吸收和反射光线。让我们先从白光说起，比如阳光(阳光并非完完全全是白色，但是对我们来说足够了)。白光包含了各种波长的光。当它撞击一个物体时，该物体的表面吸收一些波长并反射另一些波长，这取决于物体的材质。一些反射的光线照射到我们的眼睛上，我们的大脑将其转化为颜色。什么颜色？波长被表面反射的波长之和。

So what’s going on with the crayons? 你从纸上反射的白光开始。因为它是白纸，所以它能反射大部分光线。当你用“黄色”蜡笔画的时候，你添加了一层材料，它可以吸收一些波长，同时可以让另一些波长通过。它们被纸反射，再次穿过黄色层，击中你的眼睛，然后你的大脑将这种特殊的波长组合解释为“黄色”。黄色层所做的是从原始的白光中减去一束波长。

你可以把每个彩色圆看作一个过滤器: 当你画一个蓝色圆圈与黄色圆圈重叠时，你从原始光中过滤掉了更多的波长，所以你眼睛看到的是那些没有被蓝色或黄色圆圈过滤掉的波长，你的大脑看到的是“绿色”

总之，我们开始时是所有的波长，减去一些原色来创造任何其他颜色。这个颜色模型的名字来源于我们通过减去波长来创造颜色。

不过，这种模式并不完全正确。减色模型中实际的原色不是教给幼儿和艺术学生的蓝色「blue」、红色「red」和黄色「yellow」，而是青色「cyan」、洋红色「magenta」和黄色「yellow」。此外，混合这三种原色会产生一种略暗的颜色，这种颜色并不是完完全全的黑色，所以纯黑色被添加到第四种“原色「primary」”中因为 b 用来表示蓝色，黑色用 k 表示，所以我们得到了印刷四分色模式「CMYK」（图2-5)。

![Figure 1-4: The four subtractive primary colors used by printers](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries2.png)
<center>图2-5: 打印机使用的四种减色原色</center>

你可以在彩色打印机的墨盒之中直接看到这种颜色模型的迹象，在那种不同的颜色彼此略微偏移的廉价印刷的传单上。

![CMYK](/img/in-post/post-introductory/cmyk.jpg)
<center>图2-6: the different colors are slightly offset from one another</center>

### 加色法模型「Additive Color Model」

减色法模式只是这故事的其中一半。如果你曾经近距离或者用放大镜看过屏幕(又或者说某些时候，你不小心在屏幕面前打了个喷嚏) ，你可能会看到一些彩色的小点: 它们是红色「red」、绿色「green」和蓝色「blue」。

电脑屏幕和纸不同。纸张不发光，它只是反射照射它的部分光线。另一方面，屏幕是黑色的，但它自身会发光。对于纸张，我们从白光开始，减去我们不想要的波长; 对于屏幕，我们从没有光开始，然后增加我们想要的波长。

不同的原色是必要的。大多数颜色可以通过向黑色表面添加不同数量的红色「red」、绿色「green」和蓝色「blue」来创建; 这是 *RGB color model*，*加色法模型「additive color model」*，如图2-7所示。

加色法三原色的组合比其成分的颜色*lighter*，而减色法三原色的组合颜色*darker*; 加色法三原色相加得到的是白色，而减色法三原色相加得到是黑色。

![Figure 2-7: The additive primary colors and some of their combinations](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries3.png)
<center>图 2-7: The additive primary colors and some of their combinations</center>

### Forget the Details

Now that you know all this, 你可以有选择地忘记大多数细节，专注于对我们工作重要的事情.

大多数颜色可以用 RGB 或 CMYK (或者其他颜色模型)表示，并且可以从一个*color space*转换到另一个。由于我们的重点是在屏幕上渲染东西，后续使用到的为 RGB 颜色模型。

如上所述，物体吸收接触它们的部分光线并反射其余光线。哪些波长被吸收，哪些被反射，这就是我们所感知到的表面的“Color”。从现在开始，我们将简单地把Color看作是表面的一个属性，而忘记光的波长。
## 色彩深度和表现「Color Depth and Representatio」

显示器通过混合不同数量的红色、绿色和蓝色来创建颜色。它们以不同的电压照亮表面上不同强度的彩色小点，从而达到为们的预期。「image intensity」表示单通道图像像素的强度（值的大小）,RGB则包含三个image 

有多少不同的intensity是我们可以拿到的呢？虽然voltage is continuous，但我们使用计算机操纵颜色，它使用离散值(discrete values)。我们能够表现的红色、绿色和蓝色的色调越多，我们就能够生产出更多的颜色。

现在你看到的大多数图像每个primary color使用8 bits，在这里我们称之为色彩通道「*color channel*」。使用8 bits 每通道给我们24 bits每像素，总共有$2^{24}$不同的颜色(大约1670万)。这种格式被称为 *R8G8B8*或简称为*888*。我们说这种格式的颜色深度*color depth*为24位

这不是唯一的格式。之前，为了节省内存，15位和16位格式还很流行，15位格式每个通道分配5位，16位格式则是红色5位，绿色6位，蓝色5位，(称为*R5G6B5* 或 *565*格式)。绿色得到多一个bit是因为我们的眼睛对绿色的变化比红色或蓝色的变化更敏感。如图2-8,会发现，绿色的点会多过其他的

![Screen](/img/in-post/post-introductory/Screen.jpg)
<center>图 2-8: LCD频幕上的像素</center>

使用16位，我们可以得到$2^{16}$种颜色(大约65000种)。这意味着在24-bit模式下，每256种颜色只有一种颜色。虽然65,000种颜色已经足够了，但对于颜色渐变图像，你可以看到非常微妙的过程，而这些过程在16,700万种颜色中是看不到的，其中有足够的bits来表示渐变的两色之间的颜色。对于一些特殊的应用，比如电影的色彩分级，使用更多的色彩细节是一个好主意，每个*color channel*使用更多的bits。


我们将使用3个字节来表示一种颜色，每个字节包含一个8位颜色通道「8-bit color channel」的值，从0到255。我们将颜色表示为$(r，g，b)$,例如，$(255,0,0)$表示纯红色; $(255,255,255)$表示白色; $(255,0,128)$表示紫红色。

## 色彩处理「Color Manipulation」

接下来我们将处理颜色。如果你知道一些线性代数，你可以把颜色想象成三维颜色空间中的向量。

我们可以通过将每个颜色通道乘以一个常数来调整颜色的强度:\$$k(R, G, B) = (kR, kG, kB)$$

例如，$(32,0,128)$ 是 $(16,0,64)$的两倍。

我们可以将两种颜色的color channels分别地叠加在一起:
\$$(R_1, G_1, B_1) + (R_2, G_2, B_2) = (R_1 + R_2, G_1 + G_2, B_1 + B_2)$$


例如，如果我们想要组合 red $(255,0,0)$和 green $(0,255,0)$ ,我们按通道将它们相加，然后得到$(255,255,0)$ ，也就是yellow!

这些操作可能产生无效值; 例如，将$(192、64、32)$加倍,将产生超出我们颜色范围的 $R$ 值。我们将大于255的任何值视为255，小于0的任何值视为0; 我们将值限制在[0-255]的范围内。这或多或少等同于你在现实生活中拍摄一张曝光不足或过度的照片时所发生的情况: 你得到的要么是完完全全黑的要么是完完全全的白色。



## 场景「The Scene」

到目前为止，我们已经介绍了Canvas画布，一个我们可以在上面给像素上色的抽象表面。现在我们通过引入另一个抽象的概念，把注意力转移到我们感兴趣的物体上: 场景 the *scene*。

场景是你可能感兴趣的渲染对象的集合。它可以代表任何东西，从漂浮在无限空间中的单个球体(我们将从那里开始)到一个脾气暴躁的怪物鼻子内部的详细模型。

我们需要一个坐标系来讨论场景中的物体。我们不能使用与画布相同的坐标系，原因有二。首先，canvas画布是二维的，而scenes场景是三维的。其次，画布和场景使用不同的单位: 我们为画布使用pixels像素，为场景使用真实世界的单位(如英制或米制)。


轴的选择是任意的，因此我们将选择一些对我们的目的有用的东西。我们定义 $Y$ 是朝上的，$X$ 和 $Z$ 是水平的，这三个轴都互相垂直。可以把 $XZ$ 平面想象成“地板”，而 $XY$ 和 $YZ$ 平面是方形房间中的垂直“墙壁”。这与我们为画布选择的坐标系一致，$y$ 为上，$x$ 为水平。图1-6显示了它的样子。

![Figure 1-6: The coordinate system we’ll use for our scenes](https://gabrielgambetta.com/computer-graphics-from-scratch/images/coordinate-system.png)
<center>图 2-9: 我们将在scenes场景中使用的坐标系</center>

场景单位的选择有点随意，它取决于你的场景代表什么。如果你在模拟一个茶杯，“1”可能意味着1英寸，如果你在模拟太阳系，“1”可能意味着1天文单位。只要我们始终如一地使用我们选择的单位，它们是什么并不重要，所以从现在开始我们可以安全地忽略它们。

## 总结「Summary」

在本篇文章中，我们介绍了 canvas，它是一个抽象概念，表示一个我们可以绘制的矩形表面，加上一个我们将在其上构建所有东西的方法: `PutPixel`。我们还选择了一个坐标系来表示画布上的像素，并描述了一种表示这些像素颜色的方法。最后，我们引入了场景的概念，并选择了一个在场景中使用的坐标系。

```javascript
// ======================================================================
//  Low-level canvas access. 
// ======================================================================

var canvas = document.getElementById("canvas");
var canvas_context = canvas.getContext("2d");
var canvas_buffer = canvas_context.getImageData(0, 0, canvas.width, canvas.height);
var canvas_pitch = canvas_buffer.width * 4;

// The PutPixel() function.
var PutPixel = function(x, y, color) {
  x = canvas.width/2 + Math.floor(x);
  y = canvas.height/2 - Math.floor(y) - 1;

  if (x < 0 || x >= canvas.width || y < 0 || y >= canvas.height) {
    return;
  }

  var offset = 4*x + canvas_pitch*y;
  canvas_buffer.data[offset++] = color[0];
  canvas_buffer.data[offset++] = color[1];
  canvas_buffer.data[offset++] = color[2];
  canvas_buffer.data[offset++] = 255; // Alpha = 255 (full opacity)
}
```
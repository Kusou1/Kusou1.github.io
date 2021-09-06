---
layout:       post
title:        "图形学概念导论"
date:         2021-08-01 11:31:00
author:       "zhangshaohong"
header-img: ""
header-bg-css: "linear-gradient(to top, #d5d4d0 0%, #d5d4d0 1%, #eeeeec 31%, #efeeec 75%, #e9e9e7 100%);"
catalog:      true
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

x坐标的范围是 
$$ [{-C_w \over 2},{C_w \over 2}) $$ 
y 坐标的范围是 
$$ [{-C_h \over 2}, {C_h \over 2}) $$
. 我们只让 `PutPixel` 在这个范围内有效.

Canvas将绘制在屏幕上，因此我们需要从一个坐标系转换到另一个。要做到这一点，我们需要改变坐标系的中心和 y 轴的相反方向。由此得到的换算公式如下:

$$ S_x = {C_w \over 2} + C_x $$

$$ S_y = {C_h \over 2} - C_y $$



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

So what’s going on with the crayons?你从纸上反射的白光开始。因为它是白纸，所以它能反射大部分光线。当你用“黄色”蜡笔画的时候，你添加了一层材料，它可以吸收一些波长，但是可以让其他波长通过。它们被纸反射，再次穿过黄色层，击中你的眼睛，然后你的大脑将这种特殊的波长组合解释为“黄色”黄色层所做的是从原始的白光中减去一束波长。
So what’s going on with the crayons? You start with white light reflecting off the paper. Since it’s white paper, it reflects most of the light it receives. When you draw with a “yellow” crayon, you’re adding a layer of a material that absorbs some wavelengths but lets others pass through it. They’re reflected by the paper, pass through the yellow layer again, hit your eyes, and your brain interprets that particular combination of wavelengths as “yellow.” What the yellow layer does is *subtract* a bunch of wavelengths from the original white light.

You can think of each colored circle as a filter: when you draw a blue circle overlapping the yellow one, you’re filtering out even more wavelengths from the original light, so what hits your eyes is whatever wavelengths weren’t filtered by either the blue or the yellow circles, which your brain sees as “green.”

In summary, we start with all wavelengths and subtract some amount of the primary colors to create any other color. This color model gets its name from the fact that we’re creating colors by subtracting wavelengths.

This model isn’t quite right, though. The actual primary colors in the subtractive model aren’t the blue, red, and yellow taught to toddlers and art students, but cyan, magenta, and yellow. Furthermore, mixing the three primary colors produces a somewhat darkish color that isn’t quite black, so pure black is added as a fourth “primary.” Because B is used to represent blue, black is denoted by K, and so we arrive at the *CMYK color model* (Figure 1-4).

You can see evidence of this color model directly on the cartridges of color printers, or sometimes in the shapes of cheaply printed flyers where the different colors are slightly offset from one another.

![Figure 1-4: The four subtractive primary colors used by printers](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries2.png)Figure 1-4: The four subtractive primary colors used by printers

### [Additive Color Model](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#additive-color-model)

The subtractive color model is only half the story. If you’ve ever looked at a screen up close or with a magnifying glass (or, let’s be honest, accidentally sneezed on it), you’ve probably seen tiny colored dots: these are red, green, and blue.

Computer screens are the opposite of paper. Paper doesn’t emit light; it merely reflects part of the light that hits it. Screens, on the other hand, are black, but they do emit light on their own. With paper, we start with white light and *subtract* the wavelengths we don’t want; with a screen, we start with no light and *add* the wavelengths we want.

Different primary colors are necessary for this. Most colors can be created by adding different amounts of red, green, and blue to a black surface; this is the *RGB color model*, an *additive color model*, shown in Figure 1-5.

The combination of additive primary colors is *lighter* than its components, whereas the combination of subtractive primary colors is *darker*; all the additive primaries add up to white, while all the subtractive primaries add up to black.

![Figure 1-5: The additive primary colors and some of their combinations](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries3.png)Figure 1-5: The additive primary colors and some of their combinations

### [Forget the Details](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#forget-the-details)

Now that you know all this, you can selectively forget most of the details and focus on what’s important for our work.

Most colors can be represented in either RGB or CMYK (or in any of the many other color models), and it’s possible to convert from one *color space* to another. Since we’re focusing on rendering things on a screen, we use the RGB color model for the rest of this book.

As described above, objects absorb part of the light reaching them and reflect the rest. Which wavelengths are absorbed and which are reflected is what we perceive as the “color” of the surface. From now on, we’ll simply treat the color as a property of a surface and forget about light wavelengths.

## [Color Depth and Representation](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#color-depth-and-representation)

Monitors create colors by mixing different amounts of red, green, and blue. They do this by lighting the tiny colored dots on their surface at different intensities by supplying different voltages to them.

How many different intensities can we get? Although voltage is continuous, we’ll be manipulating colors with a computer, which uses discrete values (that is, a limited number of them). The more shades of red, green, and blue we can represent, the more colors we’ll be able to produce.

Most images you see these days use 8 bits per primary color, which we call a *color channel* in this context. Using 8 bits per channel gives us 24 bits per pixel, for a total of 224224 different colors (approximately 16.7 million). This format, known as *R8G8B8* or simply *888*, is the one we’ll use throughout this book. We say this format has a *color depth* of 24 bits.

This is by no means the only possible format. Not so long ago, in order to save memory, 15- and 16-bit formats were popular, assigning 5 bits per channel in the 15-bit case, and 5 bits for red, 6 for green, and 5 for blue in the 16-bit case (known as the *R5G6B5* or *565* format). Green gets the extra bit because our eyes are more sensitive to changes in green than to changes in red or blue.

With 16 bits, we get 216216 colors (approximately 65,000). This means you get one color for every 256 colors in 24-bit mode. Although 65,000 colors is plenty, for images where colors change very gradually you would be able to see very subtle “steps” that just aren’t visible with 16.7 million colors, where there are enough bits to represent the colors in between. For some specialized applications, such as color grading for movies, it’s a good idea to represent even more color detail, using even more bits per channel.

We’ll use 3 bytes to represent a color, each holding the value of an 8-bit color channel, from 0 to 255. We’ll express the colors as (R,G,B)(R,G,B)—for example, (255,0,0)(255,0,0) represents pure red; (255,255,255)(255,255,255) represents white; and (255,0,128)(255,0,128) represents a reddish purple.

## [Color Manipulation](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#color-manipulation)

We’ll use a handful of operations to manipulate colors. If you know some linear algebra, you can think of colors as vectors in 3D color space. If not, don’t worry, we’ll go through the basic operations we’ll be using now.

We can modify the intensity of a color by multiplying each of its color channels by a constant:



k(R,G,B)=(kR,kG,kB)k(R,G,B)=(kR,kG,kB)



For example, (32,0,128)(32,0,128) is twice as bright as (16,0,64)(16,0,64).

We can add two colors together by adding their color channels separately:



(R1,G1,B1)+(R2,G2,B2)=(R1+R2,G1+G2,B1+B2)(R1,G1,B1)+(R2,G2,B2)=(R1+R2,G1+G2,B1+B2)



For example, if we want to combine red (255,0,0)(255,0,0) and green (0,255,0)(0,255,0), we add them channel-wise and get (255,255,0)(255,255,0), which is yellow.

These operations can yield invalid values; for example, doubling the intensity of (192,64,32)(192,64,32) produces an RR value outside our color range. We’ll treat any value over 255 as 255, and any value below 0 as 0; we call this *clamping* the value to the [0–255] range. This is more or less equivalent to what happens when you take an under- or over-exposed photograph in real life: you get either completely black or completely white areas.

That about sums it up for our primer on colors and `PutPixel`. Before we move on to the next chapter, let’s spend a little time exploring how to represent the 3D objects we’ll be rendering.

## [The Scene](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#the-scene)

So far, we have introduced the canvas, the abstract surface on which we can color pixels. Now we turn our attention to the objects we’re interested in representing by introducing another abstraction: the *scene*.

The scene is the set of objects you may be interested in rendering. It could represent anything, from a single sphere floating in the empty infinity of space (we’ll start there) to an incredibly detailed model of the inside of a grumpy ogre’s nose.

We need a coordinate system to talk about objects within the scene. We can’t use the same coordinate system as the canvas, for two reasons. First, the canvas is 2D, whereas the scene is 3D. Second, the canvas and the scene use different units: we use pixels for the canvas and real-world units (such as the imperial or metric systems) for the scene.

The choice of axes is arbitrary, so we’ll pick something useful for our purposes. We’ll say that YY is up and XX and ZZ are horizontal, and all three axes are perpendicular to each other. Think of the plane XZXZ as the “floor,” while XYXY and YZYZ are vertical “walls” in a square room. This is consistent with the coordinate system we chose for the canvas, where YY is up and XX is horizontal. Figure 1-6 shows what this looks like.

![Figure 1-6: The coordinate system we’ll use for our scenes](https://gabrielgambetta.com/computer-graphics-from-scratch/images/coordinate-system.png)Figure 1-6: The coordinate system we’ll use for our scenes

The choice of scene units is somewhat arbitrary; it depends on what your scene represents. A measurement of “1” could mean 1 inch if you’re modeling a teacup, or it could mean 1 astronomical unit if you’re modeling the Solar System. As long as we use our chosen units consistently, it doesn’t matter what they are, so we can safely ignore them from now on.

## [Summary](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#summary)

In this chapter, we’ve introduced the canvas, an abstraction that represents a rectangular surface we can draw on, plus the one method we’ll build everything else on: `PutPixel`. We’ve also chosen a coordinate system to refer to the pixels on the canvas and described a way to represent the color of these pixels. Lastly, we introduced the concept of a scene and chose a coordinate system to use in the scene.

Having laid these foundations, it’s time to start building a raytracer and a rasterizer on top of them.
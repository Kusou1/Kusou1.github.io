---
layout: post
title: '计算机图形学二：视图变换(坐标系转化，正交投影，透视投影，视口变换)'
date: 2021-08-31 10:26:00
author: 'zhangshaohong'
header-style: text
catalog: true
tags:
    - Fundamentals of Computer Graphics
    - Math
---

> 「视图变换（Viewing Transformation）」 我们可以这样来描述视图变换的任务：将虚拟世界中以（x,y,z)为坐标的物体变换到 以一个个像素位置(x,y) 来表示的屏幕坐标系之中(2 维)，这确实是一个较为复杂的过程，但是整个过程可以被细分为如下几个步骤。

**(1) 模型变换(modeling tranformation)：这一步的目的是将虚拟世界中或者更具体点，游戏场景中的物体调整至他们应该在的位置**

**(2) 摄像机变换(camera tranformation)：在游戏中我们真正在乎的是摄像机(或者说眼睛)所看到的东西，也就是需要得到物体与摄像机的相对位置**

**(3) 投影变换(projection tranformation)：在摄像机变换之后，我们得到了所有可视范围内的物体相对于摄像机的相对位置坐标(x,y,z)，之后根据具体情况选择平行投影或是透视投影，将三维空间投影至标准二维平面([-1,1]^2)之上 （tips：这里的 z 并没有丢掉，为了之后的遮挡关系检测）**

**(4) 视口变换(viewport transformation)：将处于标准平面映射到屏幕分辨率范围之内，即[-1,1]^2![[公式]](https://www.zhihu.com/equation?tex=%5Crightarrow)[0,width]\*[0,height], 其中 width 和 height 指屏幕分辨率大小**

有了如上 4 个步骤之后，整个视图变换的过程就变的清晰了起来，如下给出一个具体例子帮助理解：

![img](https://pic2.zhimg.com/80/v2-30da233abacca39a4cf87c28ed8e3e75_720w.jpg)

上图的 4 个箭头分别对应了上文所讲述的 4 个具体步骤，接下来我们就具体讲解这 4 个步骤到底做了什么。

## **1 模型变换(modeling transformation)**

关于模型变换其实在开头部分便已经讲清楚了，就是利用基础的变换矩阵将世界当中的物体调整至我们想要的地方(旋转，平移，缩放)。

## **2 摄像机变换(camera tranformation)**

Camera transformation 属于比较复杂的一部分，但其实一但掌握了套路，也十分容易理解，希望我的解释能够帮助到你。

正如上文所说摄像机变换的目的是得到所有可视物体与摄像机的相对位置，怎么得到？非常直观的一步，我们把物体和摄像机一起做移动，如果能够把摄像机的坐标轴(假设为 u,v,w 分别对应原世界空间中的 x,y,z)移动到标准的 x，y，z 轴，那么此时物体的坐标不自然便是相对坐标了吗！

因此核心问题就变成了如何表示或者说如何将 camera 的坐标系与原世界坐标系重合呢？我们先定义 3 个东西

**相机或眼睛位置 (eye postion) e**

**观察方向 (gaze postion) g**

**视点正上方向 (view-up vector ) t**

有了这三者定义之后，我们便可以建立摄像机坐标系了，定义如下：

![img](https://pic4.zhimg.com/80/v2-b2379690a91c749d08822b9e1a76fbdb_720w.jpg)

其中 u，v，w 分别对应标准坐标系下的 x，y，z，示意图如下：

![img](https://pic1.zhimg.com/80/v2-52b293df688294aa1d1ddbfc20428f38_720w.jpg)

如此成功建立摄像机坐标系之后，如何将其移动到原世界坐标系呢？

**1 将相机位置移动至原点**

**2 通过旋转矩阵将二者坐标系重合**

第一步只需简单的减去相机位置坐标 e 即可，而第二步也曾在**[计算机图形学一：基础变换矩阵总结(缩放，旋转，位移)](https://zhuanlan.zhihu.com/p/144323332)**就以讲过，只需用![[公式]](https://www.zhihu.com/equation?tex=%28u%2Cv%2Cw%29%5ET)矩阵便可轻松表示将摄像机坐标系旋转至世界坐标系了，如果不理解的读者请移步其中 2.2 节花个 1,2 分钟读一下就能明白！

相信到这整个摄像机变换的过程已经十分清楚了，我们定义该变换矩阵为![[公式]](https://www.zhihu.com/equation?tex=M_%7Bcam%7D)定义如下：

![img](https://pic2.zhimg.com/80/v2-19c7663683167b52c01543c2e7baa6a5_720w.jpg)

（本文中所有矩阵都是左乘），最右边的矩阵代表第一步操作移至原点，旁边那个矩阵则代表第二步操作将坐标系重合。

tips：这里用了齐次坐标系表示！

## **3 投影变换(projection tranformation)**

投影变换时除了摄像机之外另外一个重点，在经过摄像机变换之后得到的依然时三维空间中的顶点坐标，如何将其映射至二维空间坐标就交给投影变换完成。正如我们所熟知的那样投影又主要分为正交投影和透视投影，依次介绍

### **3.1 正交投影变换(Orthographic Projection Transformation)**

![img](https://pic2.zhimg.com/80/v2-7f7b17cfb64e4b0c8f7a03d6420027bd_720w.jpg)

正交投影是相对简单的一种，坐标的相对位置都不会改变，所有光线都是平行传播，我们只需将物体（可视部分，即上图的那个长方体）全部转换到一个![[公式]](https://www.zhihu.com/equation?tex=%5B-1%2C1%5D%5E3)的空间之中即可（其中 x，y 坐标便是投影结果，保留 z 是为了之后的遮挡检测）

**tips：这里可能会有读者疑问，为什么要压缩到一个小立方体呢？其实这只是为了之后的计算更加的方便而已，在转换到屏幕坐标的时候就会重新拉伸回来，不必太做纠结，只需抓住正交投影的变化核心是，所有物体的相对大小位置都不会有任何变化。**

先定义可视空间如上图所示，其中：

![img](https://pic3.zhimg.com/80/v2-04e5930ac6d82713fff7d610bcb4682e_720w.jpg)

如何将这么一个范围转换为![[公式]](https://www.zhihu.com/equation?tex=%5B-1%2C1%5D%5E3)的空间呢，这其实就是一个空间的缩放，具一个简单的二维例子从![[公式]](https://www.zhihu.com/equation?tex=%5Bx_l%2Cy_l%5D%2A%5Bx_h%2Cy_h%5D%5Crightarrow%5Bx_l%5E%7B%5Cprime%7D%2Cy_l%5E%7B%5Cprime%7D%5D%2A%5Bx_h%5E%7B%5Cprime%7D%2Cy_h%5E%7B%5Cprime%7D%5D)：

![img](https://pic3.zhimg.com/80/v2-dccecc69b6651aa04f3b428ab49d2fee_720w.jpg)

图示已然很清晰，是一个更加通用的例子，从任意一个范围转换到任意一个范围，同样也是分三步

**1 将原空间范围的左下角移至原点**

**2 放大给定倍数**

**3 将缩放后的空间范围移至新空间范围**

这样一个 general 的二维例子用矩阵来表示就是这样：

![img](https://pic2.zhimg.com/80/v2-64cb7f8b8016c1f0b77d2c09dd4dd511_720w.jpg)

是不是非常直观呢，当然也可以非常简单的推广到三维，即

![[公式]](https://www.zhihu.com/equation?tex=%5Bx_l%2Cy_l%5D%2A%5Bx_h%2Cy_h%5D%2A%5Bz_l%2Cz_h%5D%5Crightarrow%5Bx_l%5E%7B%5Cprime%7D%2Cy_l%5E%7B%5Cprime%7D%5D%2A%5Bx_h%5E%7B%5Cprime%7D%2Cy_h%5E%7B%5Cprime%7D%5D%2A%5Bz_l%5E%7B%5Cprime%7D%2Cz_h%5E%7B%5Cprime%7D%5D)

这里不给出计算过程，直接给出最后变换矩阵

![img](https://pic3.zhimg.com/80/v2-b1e96e0c10c524084b9a08390ca6f252_720w.jpg)

这样得到了一个 general 的变换矩阵之后，正交投影变换只需将相应的数据带入即可，定义正交变换矩阵![[公式]](https://www.zhihu.com/equation?tex=M_%7Borth%7D)如下：

![img](https://pic3.zhimg.com/80/v2-2d702e8622fba68e4e3803237ec9e706_720w.jpg)

### **3.2 透视投影变换(Perspective Projection Transformation)**

![img](https://pic2.zhimg.com/80/v2-8253bc07238dce6782fc192f6dc3e03d_720w.jpg)

透视投影就是最类似人眼所看东西的方式，遵循近大远小，如果说正交投影都是水平光线，那么透视投影则显然不是了(左透视，右正交)

![img](https://pic1.zhimg.com/80/v2-9eb78358706fb90876fd2f6831d61454_720w.jpg)

此时，投影过程可用下图解释，将![[公式]](https://www.zhihu.com/equation?tex=%28x%2Cy%2Cz%29)一点投影至投影屏幕之后，他的坐标变为![[公式]](https://www.zhihu.com/equation?tex=%28x%5E%7B%5Cprime%7D%2Cy%5E%7B%5Cprime%7D%2Cz%5E%7B%5Cprime%7D%29)

![img](https://pic1.zhimg.com/80/v2-1bc88c0f6d01fdcdfe32ae3d51f585b4_720w.jpg)

图中原点代表视点，z = -n 代表投影平面，利用相似三角形性质不难得出图中投影之后的坐标

那么利用齐次坐标的性质，希望找到一个矩阵完成如下变换：

![img](https://pic1.zhimg.com/80/v2-a28df2ffb4eea8a7973c678dd52756e4_720w.jpg)

即：

![img](https://pic3.zhimg.com/80/v2-a5b70c8011a4f7ab189b9d9941dbeb5e_720w.jpg)

（如果形象化的描述一下的话，就是利用这个变换矩阵将整个空间压缩了一下，使其对应了真正透视投影的坐标，最后不要忘了要利用正交转换到![[公式]](https://www.zhihu.com/equation?tex=%5B-1%2C1%5D%5E3)的空间之内）

首先，这个矩阵的前两行和最后一行是能很快确定出来的，根据最后的齐次坐标，如下：

![img](https://pic1.zhimg.com/80/v2-92be32d1f59308e43dc0cd3143f46af8_720w.jpg)

那么如何确定第三行呢，这里就要运用透视投影的一个性质：

**可视空间，即本节第一张图的 Frustum 的前后面变换之后 z 坐标不变**

-   Any point on the near plane will not change
-   Any point’s z on the far plane will not change

(因为根据上述两个性质第三行的第一第二列必然为零)

所以不妨设第三行为(0,0,A,B)利用两点性质分别代入远近平面的任意两点就可以列出两个等式

![[公式]](https://www.zhihu.com/equation?tex=An+%2B+B+%3D+n%5E2)

![[公式]](https://www.zhihu.com/equation?tex=Af+%2B+B+%3D+f%5E2)

容易解出![[公式]](https://www.zhihu.com/equation?tex=A+%3D+n+%2B+f), ![[公式]](https://www.zhihu.com/equation?tex=B+%3D+-nf)。

得最后变换矩阵为

![img](https://pic4.zhimg.com/80/v2-6fb3460df4352466c0a5e6df92190e87_720w.jpg)

最后的一步，将这个被压缩过的空间，重新正交投影成标准小立方体，故定义透视投影变换![[公式]](https://www.zhihu.com/equation?tex=M_%7Bper%7D)如下

![[公式]](https://www.zhihu.com/equation?tex=%5Clarge%7BM_%7Bper%7D+%3D+M_%7Bortho%7DM_%7Bpersp%5Crightarrow-ortho%7D%7D)

其中两个矩阵都已再上文定义过。计算结果如下：

![img](https://pic4.zhimg.com/80/v2-f7dfa75940aee675599d1ddbca73ed63_720w.jpg)

## **4 视口变换(viewport transformation)**

这一步就很简单了，一开始也介绍过就是两个范围空间的转换

![[公式]](https://www.zhihu.com/equation?tex=%5B-1%2C1%5D%5E2%5Crightarrow%5B0%2Cwidth%5D%2A%5B0%2Cheight%5D%2C)

在正交变换一节已详细解释清楚，这里直接套公式即可

![img](https://pic1.zhimg.com/80/v2-f806f0339d4401b33bfeddf5f455fe8c_720w.jpg)

呼~ 至此历经上述 4 个变换，我们就已经成功的把游戏世界的任意可视物体转换到屏幕上了！回顾一下，其实也不是很难，只要分清步骤就会十分清晰。

**总结：变换 ![[公式]](https://www.zhihu.com/equation?tex=%5Clarge%7BM+%3D+M_%7Bview%7DM_%7Bper%7DM_%7Bcam%7DM_%7Bmodel%7D%7D)**

都看到这里了，如果觉得有用的话不妨点个赞吧！:)

## **Reference**

[1] Fundamentals of Computer Graphics 4th

[2] GAMES101-现代计算机图形学入门-闫令琪

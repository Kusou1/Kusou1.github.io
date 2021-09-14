---
layout:       post
title:        "Basic Raytracing"
date:         2021-08-23 11:31:00
author:       "zhangshaohong"
header-img:   "img/starWar.jpeg"
catalog:      true
mathjax:      true
tags:
    - Graphics
    - Math
---

> 光线追踪器「raytracer」和光栅化器「rasterizer」采用不同的方法将3d场景渲染到2d屏幕上。然而，这两种方法都有一些共同的基本概念。这篇文章将在canvas上的像素「pixels」来定义坐标系「coordinate system」,以及如何表示和操作颜色.

## The Canvas

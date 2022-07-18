---
layout: post
title: 'Create Liquid Effect using CSS-doodle'
date: 2022-07-18 03:26:00
author: 'zhangshaohong'
header-style: text
catalog: true
tags:
    - Web Components
---

> Creating a cool Liquid page using css-doodle

### Intro

CSS-doodle is A web component for drawing patterns with CSS,Web Components is a suite of different technologies allowing you to create reusable custom elements — with their functionality encapsulated away from the rest of your code — and utilize them in your web apps.

React and Web Components are built to solve different problems. Web Components provide strong encapsulation for reusable components, while React provides a declarative library that keeps the DOM in sync with your data. The two goals are complementary. As a developer, you are free to use React in your Web Components, or to use Web Components in React, or both.

Most people who use React don’t use Web Components, but you may want to, especially if you are using third-party UI components that are written using Web Components.

<css-doodle /> is based on [Shadow DOM v1](https://web.dev/shadowdom-v1/) and [Custom Elements v1](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements). You can use it on all major browsers right now without polyfills.

The component will generate a grid of divs by the rules (plain CSS) inside it. You can easily manipulate those cells using CSS to come up with a graphic pattern or an animated graph.

The limit is the limit of CSS itself.

### What we'll be building

Here's a preview,awesome!Do you believe? Just use css-doodle to achieve such a wonderful effect:

<iframe width="100%" height="600" src="/html/water.html" frameborder="0" allowfullscreen></iframe>

### Getting Started

Download the [latest](https://cdn.jsdelivr.net/npm/css-doodle@0.28.2/css-doodle.min.js) version or include it directly from a CDN:

```javascript
<script src="https://unpkg.com/css-doodle@0.28.2/css-doodle.min.js"></script>

<css-doodle>
  /* put your code here */
</css-doodle>
```

You can also install it from [npm](http://npmjs.org/package/css-doodle) and import the module in JavaScript:

```javascript
npm install css-doodle

/* import it */
import 'css-doodle';
```

### Time to code

```html
<style>
    html, body { 
      width: 100%;
      height: 100%; 
      margin: 0;
      display: flex; 
      align-items: center; 
      justify-content: center;
      background: radial-gradient(#459dc1,#070729); 
      overflow: hidden;
    }
</style>
<css-doodle>
    :doodle {
        @grid: 80x1 / 100vw 100vh; 
        @min-size: 100px;
        filter: url(#filter); 
        will-change: transform;
        animation: r 23s linear infinite;
      }
    
      @size: 100% 50%;
      position: absolute;
      top: 25%;
      transform: rotate(@r(360deg));
      perspective: 130px; 
    
      :after {
        content: '';
        position: absolute;
        @size: @r(10px);
        will-change: transform;
        animation: cycle @r(2s, 8s) linear infinite;
        animation-delay: -@r(100s);
        background: #fff8;
        box-shadow: @m3(0 0 calc(.5vmin + 5px) #fff);
        --trans: scaleX(@r(.1, 5)) translateZ(105px);
        transform: rotateY(0) @var(--trans);
      }
      @keyframes cycle {
        to {
          transform: rotateY(@p(-1turn, 1turn)) @var(--trans);
        }
      }
      @keyframes r {
        to { transform: rotate(@p(-1turn, 1turn)) }
      }
</css-doodle>
<svg style="width: 0; height:0">
    <filter id="filter">
      <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
      <feColorMatrix in="blur" mode="matrix" values="1 0 0 0 0  0 1 0 00  0 0 1 0 0  0 0 0 18 -7" result="goo" />
      <feBlend in="SourceGraphic" in2="goo" />
    </filter>
</svg>

```

**Nice!**

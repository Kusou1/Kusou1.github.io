---
layout: post
title: '简历'
date: 2022-07-12 12:00:00
author: 'zhangshaohong'
catalog: true
header-style: text
hidden: true

---


> Hello World! 😀

中文 ｜ [英文](/2022/07/12/resume/)

## 个人介绍

于2018年开始接触前端，喜欢了解前沿技术阅读技术文档。好奇心推动我学习新知识，对新技术有热情，喜欢自己动手去实现些有趣的想法。拥有较强的学习能力和解决问题的能力，能保证项目在周期内完成的同时也兼顾了质量。在寻找一个位置让我能够发挥我的所能，并且能够让我自身的能力得到进步，平时喜欢打羽毛球、游泳、看电影听听歌。


## 工作经验

### 广州伊智信息技术有限公司 （2021.1-2022.5）

负责公司国际板块的商城和官网，以及参与公司Sass软件的开发和CRM的开发

- The Clinica 北美医美项目官网 [https://theclinica.ca/](https://theclinica.ca/)(Next.js)
  - 为北美医美项目制作官网，用于品牌的营销
  - 与北美设计师对接，负责每个月的广告页的更新维护与上线
  - 全站设计改版的开发及动画效果，中英文 i18n
  - 后端使用了headless CMS Strapi，设计创建表为前端提供RESTful Api接口，其后台管理可供运营人员进行操作完成对网站内容的编辑。并且通过其i18n插件，管理不同语言的内容，通过接口更改locales完成语言切换。(接口外的内容比较少且很少更改，因此采用Excel表格管理，通过node解析生成json使用)。
  - 动画库使用了React Spring高度还原设计所需效果。
- The Clinica 商城 [https://shop.theclinica.ca/](https://shop.theclinica.ca/) (Vue3,Liquid,Tailwindcss)
  - 负责整个商城从0到1的开发，根据设计稿开发并适配多端、完成动画效果、页面交互、设置店铺、上架商品、性能优化等等工作，与海外团队对接完成交付并上线。
  - 该商城基于Shopify建站程序，使用了Shopify Theme Lab创建Shopify主题，页面使用Vue3组件化开发，UI库用Element Plus按需导入，数据层通过Liquid语言将商城数据包成对象结构传入Vue组件中使用，Vuex作为状态管理工具，最终通过Webpack打包到Shopify主题中完成页面展示。将主要开发语言使用了前端熟悉的Vue，不仅组件化工程化方便管理，大大降低了开发难度。
  - 所有页面的内容部分，数据和图片以及产品均通过Liquid语言将其绑定到Shopify店铺后台中。后期运营人员可直接在商城的后台管理页面上要显示的文字及图片，修改商品信息，监控流量，添加导入评论。动画库使用了AOS，配置了动画要求所需的参数，定义了许多动画属性满足各个页面组件复用。
- Protecia 商城 [https://proteciaskincare.ca/](https://proteciaskincare.ca/) (Vue3,Liquid,Tailwindcss)
  - 延续了The Clinca商城的开发模式，开发的新品牌shopify商城，完成动画效果的同时，兼顾多端的和谐，与海外团队对接完成交付并上线
- 伊智软件 [https://sy.meimeifa.com/](https://sy.meimeifa.com/#/) (Vue2)
  - 这是一个专门为美业商家设计的Sass软件，辅助商家的日常管理、提升效率、营销、定制App等等。
  - 项目采用TypeScript 和 Vue Composition API保障类型检测。
  - 项目的代码管理使用yarn workspace + lerna的 monorepo 策略
  - 使用swagger的api文档生成前端的接口代码
  - 参与产品需求分析，产品模块的概要设计。
  - 高质量编码还原UI设计的界面，同后端合作实现数据的交互，功能上的优化。
  - 负责伊智店务系统多国语言化i18n
  - web端调起摄像头扫描二维码功能开发，原先软件的扫码支付功能是靠原生应用桥接，提供的能力，实现了不在app下，也能使用扫码支付
- 剧本杀业务DM端（React，ant-design）
  - 这是一个为剧本杀门店DM所开发的应用，辅助DM的日常管理及游戏管理，更好的管理客户，提升效率
  - 采用TypeScript、React及Ant-design开发，在Antd的基础上，根据业务进行组件的二次封装，方便各个页面复用

### 厦门鹿课教育有限公司 （2020.6-2020.12）

- 技能盒子(Vue,PHP)
  
  - 为教培机构提供数字化内容、技术与服务，囊括教学核心业务流程的方方面面，基于职业功能模块项目式教材一体化教学
  
  - 前端使用Vue及ElementUi，使用Echart和AntV完成前端可视化的工作，使用PHP完成部分后端工作
  
  - 后台管理页面使用vue-element-admin进行二次开发
  
  - 配合产品和项目主管不断改进完善项目和提升用户体验，完成UI设计的想法及效果


## 个人项目

### Kusou1 Bank & Coin(Ethereum)

`React` `tailwindcss` `ethers.js` `solidity`  `hardhat` `openzeppelin`
Bank地址：[https://bank-dapp-osbwi635o-aka-mosthappyman.vercel.app/](https://bank-dapp-osbwi635o-aka-mosthappyman.vercel.app/)（以太币银行）

Coin地址：[https://kusou1-coin.vercel.app/](https://kusou1-coin.vercel.app/) （以太坊代币）

- 前端使用React，及tailwindcss，通过ethers.js与以太坊交互，由于以太坊交易需要间隔，采用swr进行数据请求来保证数据的实时性
- 合约通过alchemy部署在rinkeby测试链上，使用hardhat，方便合约的开发调试及部署，openzeppelin保证合约的可靠及方便开发

### 个人博客

`Javascript` `grunt` `ruby` `liquid`

地址：[https://kusou1.me](https://kusou1.me)

- 个人用于分享的博客

- 通过google-analytics进行流量数据的监控

- 通过sitemap对seo进行优化

## 技能

- 熟练掌握HTML5/CSS3,PC端和移动端
- 了解ES6/ES7
- 熟悉Node.js，使用过express,Koa2,Egg.js框架开发服务端，了解GraphQL及Apollo
- 了解MongoDB及Redis,了解Serverless
- 熟悉Antd Design,Element UI,Vant UI,tailwindcss等常用前端UI框架，了解Style-Component，chakraUI
- 熟悉React-Native开发,熟悉小程序开发及uni-app
- 了解Nuxt.js,Next.js,Gridsome,Gatsby等服务器端渲染的框架
- 了解Hybrid开发以及Electron桌面开发,及Flutter原生App开发
- 熟悉Web3相关的开发工具，hardhat,openzeppelin,web3.js,ethers.js,solidity,了解ERC20及ERC721
- 熟悉Git进行团队协作，具备良好的前端工程化思想，熟练使用Webpack、grunt等打包工具,了解微前端架构，webpack模块联邦
- 了解前端自动化测试，及Web Components
- 熟悉性能优化的方案，使用LightHouse,WebPageTest,浏览器DevTools等进行性能测试
- 熟悉前端可视化解决方案，如D3.js,Three.js,AntV,Echarts.js等


## 教育

**厦门工学院 ｜ 信息与计算科学**

**2016-2020**


## 联系方式

WeChat: 13305969555

Phone: +86 13305969555

Email: zhangshaohong1997@gmail.com




> 期待有机会能与您共事.

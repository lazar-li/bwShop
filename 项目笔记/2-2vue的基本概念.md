## 1. 前端工程化

**前端工程化**是使用软件工程的技术和方法来进行前端的开发流程、技术、工具、经验等规范化、标准化，其主要目的***为了提高效率和降低成本，即提高开发过程中的开发效率，减少不必要的重复工作时间\***，而前端工程本质上是软件工程的一种，因此我们应该从软件工程的角度来研究前端工程。

"前端工程化"里面的工程指**软件工程**，和我们一般说的**工程**是两个完全不同的概念。

- **工程**是个很泛泛的概念，甚至可以认为建了一个git仓库就相对于新建了一个工程；
- **软件工程**的定义是: "应用计算机科学理论和技术以及工程管理原则和方法，按预算和进度，实现满足用户要求的软件产品的定义、开发、和维护的工程或进行研究的学科"（GB/T11457-2006《信息技术 软件工程术语》）。

### 如何做"前端工程化"?

https://www.jianshu.com/p/88ed70476adb

## 2. 数据双向绑定

![img](https://upload-images.jianshu.io/upload_images/4111182-c0b9c7c96f93a2f0.png?imageMogr2/auto-orient/strip|imageView2/2/w/708/format/webp)

首先我们了解到Vue采用的是**发布者-订阅者模式**来实现双向数据绑定，这里我们就需要了解其中的两个角色，发布者和订阅者。

![img](https:////upload-images.jianshu.io/upload_images/4111182-47b4a6c21026a3bd.png?imageMogr2/auto-orient/strip|imageView2/2/w/831/format/webp)

发布者-订阅者模式


 这里所说的data中的某个属性，就对应着上面代码的someStr，someStr拥有一个调度中心Dep，而每个挂载着someStr属性的节点，就是所谓的订阅者（watcher），每当someStr的值发生了改变，someStr属性的setter函数就会被触发，然后Dep通知各个订阅者更新视图。

那么我们现在需要知道为什么vm的data属性上会有setter，为什么每个节点都会变成订阅者，这些是如何实现的？来看`mvvm.js`入口文件[详细代码](https://link.jianshu.com?t=https://github.com/yozosann/mvvm-/blob/master/mvvm.js)做了什么：

![img](https:////upload-images.jianshu.io/upload_images/4111182-97b4dc049f4f8b62.png?imageMogr2/auto-orient/strip|imageView2/2/w/454/format/webp)

mvvm.js


 将编译mvvm上的#mvvm-app元素及其子元素，遍历`data: {someStr: 'hello'},`添加getter与setter。

编译元素又具体是如何处理的呢（[详细代码](https://link.jianshu.com?t=https://github.com/yozosann/mvvm-/blob/master/complie.js)）：

![img](https:////upload-images.jianshu.io/upload_images/4111182-e240f4d5b964060c.png?imageMogr2/auto-orient/strip|imageView2/2/w/790/format/webp)

complie.js


 执行到最后到元素会进行实例化watcher，在了解实例化流程之前，我们先了解一下data是如何进行具体添加getter 和 setter的（[详细代码](https://link.jianshu.com?t=https://github.com/yozosann/mvvm-/blob/master/observer.js)）：

![img](https:////upload-images.jianshu.io/upload_images/4111182-389af578070a42c0.png?imageMogr2/auto-orient/strip|imageView2/2/w/731/format/webp)

observer.js

那么实例化watcher会进行哪些操作，又会和getter，setter有什么交互呢？（[详细代码](https://link.jianshu.com?t=https://github.com/yozosann/mvvm-/blob/master/watcher.js)）

![img](https:////upload-images.jianshu.io/upload_images/4111182-51da9d0896b427b2.png?imageMogr2/auto-orient/strip|imageView2/2/w/654/format/webp)

watcher.js



## 3. 组件化开发

https://www.cnblogs.com/zx0319/p/11015804.html

## vue开发的几个概念

### 1.webpack

“Webpack 是一个开源的前端打包工具。Webpack 提供了前端开发缺乏的模块化开发方式,将各种静态资源视为模块,并从它生成优化过的代码。 Webpack 可以从终端、或是更改 webpack.config.js 来设置各项功能。 要使用 Webpack 前须先安装 Node.js。”

### 2. vue , vuex , vue-router，axios

- vue

  - ```
    Vue.js是一套构建用户界面的渐进式框架，Vue 采用自下向上增量开发的设计，其核心库只关注视图层，易于上手，同时vue完全有能力驱动采用单文件组件和 Vue 生态系统支持的库开发的复杂单页应用。
    ```

    

- vuex

  - ```
    Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
    ```

    

- vue-router

  - ```
    vue-router是Vue.js官方的路由插件，它和vue.js是深度集成的，适合用于构建单页面应用。vue的单页面应用是基于路由和组件的，路由用于设定访问路径，并将路径和组件映射起来。传统的页面应用，是用一些超链接来实现页面切换和跳转的。在vue-router单页面应用中，则是路径之间的切换，也就是组件的切换。路由模块的本质 就是建立起url和页面之间的映射关系。
    ```

    

- axios

  - ```
    Axios 是一个基于 promise 的 HTTP 库，简单的讲就是可以发送get、post请求。说到get、post，大家应该第一时间想到的就是Jquery吧，毕竟前几年Jquery比较火的时候，大家都在用他。但是由于Vue、React等框架的出现，Jquery也不是那么吃香了。也正是Vue、React等框架的出现，促使了Axios轻量级库的出现，因为Vue等，不需要操作Dom，所以不需要引入Jquery.js了。
    ```

### 3. ES6 , babel

- ES6 

  - ```
    ECMAScript 6.0（以下简称 ES6）是 JavaScript 语言的下一代标准，已经在 2015 年 6 月正式发布了。它的目标，是使得 JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。
    ```

    

- babel

  - ```
    javascript在不断的发展，各种新的标准和提案层出不穷，但是由于浏览器的多样性，导致可能几年之内都无法广泛普及，babel可以让你提前使用这些语言特性，他是一种用途很多的javascript编译器，他把最新版的javascript编译成当下可以执行的版本，简言之，利用babel就可以让我们在当前的项目中随意的使用这些新最新的es6，甚至es7的语法。说白了就是把各种javascript千奇百怪的语言统统专为浏览器可以认识的语言。
    ```

    


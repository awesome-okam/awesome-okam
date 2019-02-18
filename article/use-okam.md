# 使用 okam 小程序开发框架快速开发百度智能小程序

## Okam 简介
[奥卡姆](https://github.com/ecomfe/okam)，一个面向小程序开发的开发框架，开发体验类 `Vue`, 目前已支持所有主流小程序平台的开发，包括百度小程序、微信小程序、支付宝小程序。

* 开发模式
    * 支持单文件组件化开发方式，目录结构更加清晰简洁
    * 支持 NPM 包的依赖管理和引用
    * 提供渐进增强可配置方式，包括可配置的构建流程，来提升开发框架能力

* 开发语法
    * 模板：支持类 `Vue` 模板语法、`Pug` 模板语法
    * 样式：支持 CSS 预处理器和后处理器， `Stylus`、`Less`、`Sass`、`Postcss`
    * 脚本：支持 `Typescript`、`ES Next` 语法支持

* 扩展的能力
    * `HTML` 标签支持
    * `Vue` 数据操作语法，包括 `computed`、`watch` 支持
    * `Redux` 数据状态管理
    * 模板组件 `ref` 引用支持
    * `Mixins` 支持
    * `Promise`、`Async`、`await` 语法支持
    * 原生接口支持可定制的 `Promise` 化
    * 提供原生接口 `Hook` 能力
    * 样式 `rpx` 单位自动转换
    * 接口 `Mock`
    * 图片自动压缩能力

* 其它
    * 框架 `API` 优化及扩展、数据操作优化
    * 微信请求优化
    * 原生小程序和开发框架语法混用支持
    * `cli` 快速构建

本篇文章，主要着重介绍基于 `okam` 框架进行百度小程序的开发。如果你对 `Vue` 比较熟悉或者之前使用过 `wepy`、`mpvue` 小程序开发框架，对于上手 `okam` 应该是一件很容易的事情，不需要太多额外学习成本，更多关于该框架的使用可以参考[官方文档](https://ecomfe.github.io/okam)。

## 使用 `okam` 快速开发百度智能小程序

### 准备工作

* Node 安装（要求 `Node >=8 && NPM >= 3`），具体安装可以到[官网下载](https://nodejs.org)

* CLI 工具安装:  `npm install okam-cli -g`

* 安装成功后，可以命令行输入 `okam` 回车，如果有看到相应的命令使用信息，说明已经安装成功

### 创建项目

安装成功后，可以通过命令行方式，快速初始化百度小程序项目：

```
okam init my-okam-project

cd my-okam-project

npm install

```

### 运行调试

* 百度小程序运行命令

    * `npm run dev`：           带 watch 开发模式

    * `npm run dev:clean`：     删掉构建产物（不包括项目配置文件）并重新构建且带 watch 开发模式

    * `npm run dev:server`：    带 watch && 开发 Server 开发模式

    * `npm run build`：         删掉构建重新构建（没有 watch && 开发 Server）

    * `npm run prod`：          生产环境构建


* 下载[开发者工具](https://smartprogram.baidu.com/docs/develop/tutorial/index_first/)，打开开发工具，打开项目构建目录：`开发工具` -> `打开` -> `<项目Root>/dist`

* 等待开发工具编译，就可以看到预览效果

### 目录结构

项目初始化后，会生成如下项目结构，建议项目开发过程中按如下方式进行代码的组织。

```
.
├── README.md             // 项目说明文件
├── ci.yml                // 项目 CI 配置
├── package.json          // 项目包配置信息
├── doc                   // 放置项目源图片或者其它文档目录
├── dist                  // 百度小程序 构建产物，开发工具得选择该构建产物目录作为项目根目录方能预览
├── .tinyimgcache         // 自动生成，图片压缩的缓存信息，不可删掉，否则会导致图片重复压缩
├── project.json5         // 小程序项目配置文件，除了语法使用 JS 对象形式，配置说明参考官方小程序说明
├── scripts               // 构建相关脚本
│   ├── build.js          // 构建入口脚本
│   ├── build.sh          // CI 脚本
│   ├── base.config.js    // 基础构建配置文件
│   └── swan.config.js    // 百度 Swan 小程序构建配置文件
└── src                   // 项目源码
    ├── app.js            // 小程序入口脚本
    ├── app.styl          // 小程序入口样式
    ├── common            // 项目公共代码
    │   ├── img           // 项目图片资源
    │   ├── tpl           // 项目公共模板文件
    │   └── ...
    ├── components      // 自定义组件
    └── pages           // 小程序页面集合
        ├── home        // 首页
        ├── ...
        └── ...
```

### Okam 实现机制简介

目前 `okam` 核心实现有两个主要模块：

* `okam-build`: 提供核心代码构建以及其它辅助开发能力，包括增量构建、开发 Server 等；
* `okam-core`: `okam` 核心运行时框架，运行于原生小程序框架之上，规范化了小程序页面和组件定义方式，包括生命周期、API 跟 Vue 对齐。

`okam` 框架给小程序提供的一系列框架扩展能力，都是依托于构建配置文件，该构建配置不同于其它开源框架，不仅仅是用于声明代码转译构建处理流程，还可以用于声明框架要使用的扩展能力及可以灵活的对小程序 API 和 组件进行扩展声明。这样设计，也使得开发者可以更加灵活控制自己框架要使用的能力，同时可以灵活的扩展自己的框架 API 和组件，可以更容易在不同平台间实现无缝对齐，减少了各种平台代码特殊处理逻辑。

因此，虽然我们提供了 Vue 的数据操作方式包括 `Computed` `Watch` 能力，开发者完全也可以把这个能力从核心框架移除掉。基于这种方式，开发者可以真正实现按需增强自己开发框架能力。而 `okam` 框架构建是基于依赖分析进行构建的，因此完全不用担心不同平台代码或者没用到的框架扩展能力会被构建到目标平台里。

### 构建配置文件
下面是构建配置文件大概的样子，完整的构建配置定义可以参考[这里](https://ecomfe.github.io/okam/#/build/index)。

```
const path = require('path');
module.exports = {
    verbose: false,
    root: path.join(__dirname, '..'),
    output: {
        dir: 'dist',
        depDir: 'src/common'
    },
    component: {
        extname: 'vue'
    },
    framework: [ // 框架扩展配置
        'data', 'watch', 'behavior', 'ref'
    ],
    designWidth: 750,
    processors: { // 处理器配置
       postcss: {
            extnames: ['styl'],
            options: {
                plugins: ['px2rpx', 'env']
            }
        }
   },
   dev: { // 开发环境的附加配置
   }
};
```

### 入口脚本 - `app.js`

相比原生小程序包括目前开源的一些开发框架，你要做的事情非常简单，导出入口脚本定义，如下所示：

```javascript
export deafult {
    config: {}, // 原生小程序 app.json 配置都定义在这里
    // 其它定义同原生
};
```

对于页面组件也是一样的比较简洁，不需要原生各种 `App`/`Component`/`Page` 的包裹，也不需要各种烦人扩展定义方式： `import App from 'xx'; class MyApp extends App {};`

如果你想使用各种新的 `ES Next` 语法，包括 `asyn await` 语法，这些都是支持的，你唯一要做的事情，只是变更构建配置，代码不需要额外引入任何东西，剩下的就交给开发框架来做。

### 入口样式 - `app.css`

入口样式跟原生保持一致，此外提供了可选的预处理语言供选择，包括 `stylus` `less` `sass` ，开发者可以自行选择自己喜欢的。

如果 `rpx` 单位转换让你头痛，可以使用 `okam` 提供的 [px2rpx](https://ecomfe.github.io/okam/#/advance/rpx) 插件，来帮你自动完成这个事情。

### 页面定义

相比原生开发，一个页面往往需要最多四个同名文件定义，而在 `okam` 框架里，只需要一个单文件组件同 `Vue` 就可以轻松完成一个页面定义。当然，定义的页面需要在 `app.js` `config.pages` 属性里声明，方能访问到。

```
<template>
<view class="page-wrap"></view>
</template>
<script>
import mixinA from '../common/mixinA';
import MyComponent from '../components/MyComponent';
export default {

    // 同原生小程序页面配置定义
    config: {
       title: 'page title' // title 等价于原生的 navigationBarTitleText 配置
    },

    // 声明依赖的自定义组件，不需要使用原生 usingComponents 定义
    components: {
        MyComponent
    },

    // mixin 支持
    mixins: [mixinA],

    data: {},     // 页面数据定义
    computed: {}, // 声明计算属性
    watch: {},    // 声明要 watch 数据、属性

    // 生命周期钩子
    created() {},
    mounted() {},
    destroyed() {},

    // 页面原生钩子
    onShow() {},
    onHide() {},

    // 其它自定义方法，包括事件监听处理器定义
    methods: {
      onClick() {}
    }
}
</script>
<style lang="stylus">
.page-wrap
</style>
```

## 自定义组件定义

为了更好实现组件复用功能，同时让整个页面结构更清晰一点，`okam` 框架提供了自定义组件支持，在自定义组件实现上是基于小程序提供的原生组件实现的，而不是像 `wepy` 这些早期框架基于模板方式实现。在 `okam` 框架里，不管是页面还是自定义组件都被抽象成 `Vue` 单文件组件形式。页面和自定义组件两者定义基本一样，除了自定义组件，多了个 `props` 属性定义外。

`okam` 虽然扩展了组件定义方式，但也支持原生的自定义组件使用，尤其当我们依赖组件可能是来源于第三方组件时候，需要通过 `NPM` 安装，在 `okam` 里，你完全可以不用特别区分是基于 `okam` 语法还是原生语法定义的组件，使用方式完全是一致的。

```
<template>
    <view>
        <npm-component @hi="handleHi"></npm-component>
    </view>
</template>
<script>
import NpmComponent from 'npm-component';
export default {
    components: {
        NpmComponent
    },
    methods: {
        handleHi() {}
    }
}
</script>
```

## 更强大灵活的模板语法

原生小程序模板语法使用上不够简洁灵活，为此 `okam` 对模板语法重新做了优化，基本对齐 `Vue`，除了指令少了 `v-` 命名空间前缀外，其它的使用基本一致跟 `Vue`，由于原生实现受限，目前支持的只是 `Vue` 模板语法一个子集，完整支持介绍可以参考[文档](https://ecomfe.github.io/okam/#/template/syntax)。

```
<template>
    <view>
       <button @click="handleClick('hi')">Click me</button>
       <view for="num in arr">{{num}}</view>
       <view if="flag">A</view>
       <view else>B</view>
    </view>
</template>
<script>
export default {
    data: {
        arr: [1, 23],
        flag: false
    },
    methods: {
        handleClick(info) {
             console.log(info); // hi
        }
    }
}
</script>
```

## 使用原生小程序提供的 API

小程序提供了丰富的 API，使得小程序的能力得以增强，为了使用小程序提供的 API，只需要在对应的 `App` `Page` `Component` 实例上下文访问 `this.$api.xxx` 方式访问相应的小程序 API 即可，等价于 原生小程序  `swan.xxx` 访问方式。 之所以这么设计，是为了保证框架代码平台无关性，为后续一套代码能够迁移到不同平台做准备。

```
// app.js
export default {
    onLaunch() {
        let result = this.$api.getSystemInfoSync();
        // ...
    }
}
```

如果想框架外部访问 API，可以通过如下方式：
```
import {api} from 'okam-core/na/index';
// ...
```

如果想扩展框架 API，一样的也是可以基于构建配置方式，在 `swan.config.js` 配置文件 `api` 配置项加入如下配置：

```javascript
// common/api/hi.js
// 要注入的 hi api 定义
export default function hi() {
    console.log('hi');
}
```

```javascript
// swan.config.js 构建配置里增加 api 配置项
module.exports = {
    api: {
        hi: './common/api/hi'
    }
};
```

## 数据请求

数据请求是我们业务开发中经常需要面临的场景，`okam` 框架封装了原生请求接口，优化了接口使用。

* 默认组件、页面或者 App 上下文提供了一个封装好的请求对象: `$http`

* 该请求对象请求接口默认返回的是 `Promise`，建议开发过程中直接使用该请求对象发起 HTTP 请求

* 请求对象接口

    * `$http.get(url, options)`: 发起一个 `GET` 请求，请求选项可以参考原生请求接口[定义](https://smartprogram.baidu.com/docs/develop/api/net_request/)

    * `$http.post(url, options)`: 发起一个 `POST` 请求

    * `$http.fetch(url, options)`: 发起自定义 Method 请求

        ```javascript
        this.$http.fetch(
            'http://xxx.com/xx',
            {method: 'PUT', data: {}}
        ).then(
            res => console.log(res),
            err => console.error(err)
        );
        ```

一般开发过程中，如果后端接口没有 ready，一般需要前端自己构造数据接口，为了方便开发过程中自测，`okam` 框架提供请求 mock 机制来完成请求接口自测，具体可以参考[文档](https://ecomfe.github.io/okam/#/advance/mock)。

## 更多详细信息
* [百度智能小程序文档](https://smartprogram.baidu.com/docs/develop/tutorial/codedir/)
* [OKAM 使用文档](https://ecomfe.github.io/okam/#/)
* [OKAM 上手开发](https://ecomfe.github.io/okam/#/intro/quickStart)
* [OKAM 源码地址](https://github.com/ecomfe/okam)

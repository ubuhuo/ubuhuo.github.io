---
title: webpack 4 入门
date: 2019/3/5
categories:
  - 1-网络笔记
tags:
  - 学习笔记
  - webpack
copyright: true
---

## 导读

写这篇文章是为了让自己在自学 `webpack` 的过程中有所产出，于是边读 [webpack 中文文档][4] 边写下了这篇文章，里面的很多实例都是直接挪用的文档中的实例，但在一些概念的理解上我加入了自己的想法「未必精确」，所以读的时候要抱着「怀疑的态度」。

文章内容不仅仅是简单的「概念堆叠」，还有一些「重点」概念的「深入理解」，不过篇幅有限我不希望这篇文章变成一份冗长的伪文档，所以全部的内容都是围绕 `webpack` 的 4个 核心概念延展开来的，每个配置后面我都会尽量跟上一个实例以更加形象的展示配置的具体作用。

站在我的角度上，读完这篇文章并不能让你精通 webpack 但是理解 webpack 中的重要概念，自己编写一个 webpack.config.js 配置文件还是可以的。

## webpack 简介

> 本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(static module bundler)。在 webpack 处理应用程序时，它会在内部创建一个依赖图(dependency graph)，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。
> 
> 来自 [webpack 中文文档][1]

目前都是使用一些成熟的 `CLI` 工具，一般都内置 `webpack` 所以我对 `webpack` 的认知一直比较少，只是大概的了解它是用来管理项目中的 `.js` 文件依赖，然后打包整个项目的。

## 核心概念

### 1. 入口(entry)

对应属性：`entry`
默认值：`./src/index.js`

作用说明：
用来规定 `webpack` 应该使用哪个模块作为构建内部依赖图的起点。
`webpack` 会找出所有「入口模块」（直接或间接）依赖的「模块」和 [library]。

代码示例：
```javascript
// weboack.config.js
module.exports = {
  entry: './path/to/entry/file.js'
}
```

### 2. 出口(output)

对应属性：`output`
主输出文件默路径：`./dist/main.js`
其他文件默认路径：`./dist/<filename>`

作用说明：
用来规定 `webpack` 在那里输出 `bundles` 以及如何命名这些文件。

```javascript
// weboack.config.js
const path = require('path') // Node.js 核心模块，用于操作文件路径

代码示例：
module.exports = {
  entry: './path/to/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '<WhateverYouLike>.js'
  }
}
```

### 3. 处理器(loader)

对应属性：`module->rules`

作用说明：
作为开箱即用的自带特性，`webpack` 自身只支持处理 `JavaScript` 文件。而 `loader` 能够让 `webpack` 处理那些非 `JavaScript` 文件，并且先将它们转换为有效「模块」，然后添加到「依赖图」中，提供给应用程序使用。

属性特征：
1. `test`: 利用「正则表达式」规定 `loader` 用于哪些或哪个文件。
2. `use`: 规定运行时使用哪个 `loader`。

代码示例：
```javascript
// webpack.config.js
const path = require('path')

module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: 'raw-loader'
      }
    ]
  }
}
```

> 代码作用：
> 当运行包含 .txt 文件的 require() 或 import 语句时，在它打包之前，先使用 raw-loader 转换。

### 4. 插件(plugins)

对应属性：`plugings`

作用说明：
打包优化、资源管理和注入环境变量。

代码实例：
```javascript
// webpack.config.js
const HtmlWebpackPlugn = require('html-webpack-plugin') // 提前通过 npm 安装
const webpack = require('webpack') //用于访问内置插件

module.exports = {
  ...
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
}
```

## 核心概念解析及拓展

### 1. 入口(entry)

#### 单入口及其简写

```javascript
// webpack.config.js
module.exports = {
  entry: {
    main: './path/to/entry/file.js'
  }
}

// 可简写为如下形式
module.exports = {
  enrty: './path/to/enrty/file.js'
}

/*
 * 当你需要为只有一个入口的应用程序或工具（library）快速设置 webpack 配置时，
 * 简写会是个很不错的选择。然而，使用此语法在扩展配置时有失灵活性。
 */
```

思考：当你向 `entry` 传入一个数组时会发生什么？
解释：向 `entry` 传入「文件路径数组」将创建「多个主入口」。在你想要多个依赖文件一起注入，并且将它们的依赖导向到一个 `chunk` 时，传入数组的方式就很有用。

#### 对象语法

用法：`entry: {<enrtyChunkName: String>: <Path: String | Array>}`

```javascript
// webpack.config.js
module.exports = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
}

// 对象语法会比较繁琐。然而，这是应用程序中定义入口的最可扩展的方式。
```

#### 常见场景

##### 1. 分离应用程序主体和第三方库

```javascript
// webpack.config.js
module.exports = {
  entry: {
    app: './src/app.js'
    vendors: './src/vendors.js'
  }
}

/*
 * webpack 从 app.js 和 vendors.js 开始创建依赖图。
 * 这些依赖图是彼此完全分离、互相独立的（每个 bundle 中都有一个 webpack 引导）。
 * 这种方式比较常见于，只有一个入口起点（不包括 vendor）的单页应用程序中。
 * 
 * 此设置允许你使用 CommonsChunkPlugin 从应用程序依赖图中提取 vendor 到 vendor 依赖图，并把引用 vendor 的部分替换为 __webpack_require__() 调用。
 * 如果应用程序依赖图中没有 vendor 代码，那么你可以在 webpack 中实现被称为长效缓存的通用模式。
 * 说实话，目前看不懂上面这段话，所以也不晓得怎么通俗的表述。
 */
```

##### 2. 多页面应用程序

```javascript
// webpack.config.js
module.exports = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
}

/*
 *  webpack 分离 3 个的依赖图
 *
 * 在多页应用中，每当页面跳转时服务器将为你获取一个新的 HTML 文档。
 * 页面重新加载新文档，并且资源被重新下载。这给了我们特殊的机会去做很多事：
 * 使用 CommonsChunkPlugin 使所有页面的应用程序共享代码创建依赖图，
 * 入口增多，多页应用能够复用不同入口的大量重复代码/模块。
 */
```

### 2. 出口(output)

注意，即使可以存在多个入口，但只配置一个出口设置。

#### 用法

在 `webpack` 中配置 `output` 的最低要求是，将它的值是一个包括以下两点的对象：

1. `filename`: 输出文件的文件名。
2. `path`: 输出目录的绝对路径。

```javascript
// webpack.config.js
module.exports = {
  output: {
    filename: '<WhateverYouLike>.js',
    path: '/path/to/project'
  }
}

// 此配置将一个单独的 .js 文件输出到 /path/to/project 目录中。
```

#### 配合多个入口设置

如果配置创建了多个单独的入口，则应该使用 [占位符][3] 来确保每个文件具有唯一的名称。

```javascript
// webpack.config.js
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
};

// 写入到硬盘：./dist/app.js, ./dist/search.js
```

#### 常用占位符

内部ID：`[id]`
入口名称：`[name]`
基于构建的hash（每次构建都会改变）：`[hash]`
基于内容的hash（文件内容改变才会改变）：`[chunkhash]`

#### 高级进阶

官网所谓高级进阶其实就是利用哈希占位符构建随版本迭代的文件命名方式这里不展示了。

比较有用的是如何动态设置 `publicPath`:

##### 首先，何为 publicPath，以及周边概念


1. `output.publicPath`: 所有资源的基础路径，它被称为公共路径，以 `/` 结束，示例：

```javascript
// webpack.config.js
module.exports = {
  ...
  output: {
    publicPath: '/assets/',
    chunkFilename: '[id].chunk.js'
  }
};

/*
 * HTML loader 输入出：<link href="/assets/spinner.gif" />
 * CSS：background-image: url(/assets/spinner.gif);
 * 静态资源最终访问路径 = output.publicPath + loader 或插件等配置路径
 */
```

2. `devServer.publicPath`: 确定从哪里提供 bundle

假设服务器运行在 `http://localhost:8080` 并且 `output.filename` 被设置为 `bundle.js`。默认 `publicPath` 是 `/`，所以你的包可以通过 `http://localhost:8080/bundle.js` 访问。

可以修改通过 `devServer.publicPath` 来修改请求资源时的服务器前缀，示例：

```javascript
// webpack.config.js
module.exports = {
  ...
  devServer: {
    publicPath: '/assets/'
  }
};

/*
 * 现在可以通过 http://localhost:8080/assets/bundle.js 访问 bundle。
 * 确保 publicPath 总是以斜杠(/)开头和结尾。
 * devServer.publicPath 也可以是一个完整的 URL。
 * 一般情况下都要保证 devServer.publicPath 与 output.publicPath 保持一致。
 */
```

3. `devServer.contentBase`: 告诉服务器从哪里提供内容，只有在提供静态文件时才需要

默认情况下，将使用当前工作目录作为提供内容的目录，但是你可以修改为其他目录，示例：

```javascript
// webpack.config.js
module.exports = {
  ...
  devServer: {
    // 推荐使用绝对路径。
    contentBase: path.join(__dirname, 'public')
  }
};

// 也可以从多个目录提供内容
module.exports = {
  ...
  devServer: {
    contentBase: [path.join(__dirname, 'public'), path.join(__dirname, 'assets')]
  }
};

// 具体作用不详，官网并没有给出说明也懒得查了
```

##### 其次，如何动态设置 publicPath

```javascript
// webpack.config.js
...
const BASE_URL = process.env.NODE_ENV === 'production'
  ? '/'
  : '/'

module.exports = {
  ...
  publicPath: BASE_URL,
  ...
}
// 方法来自 iview-admin vue.config.js
// 我不知道我理解的动态设置对不对，不过官网给的 __webpack_public_path__ 我没看明白
```

### 3. 处理器(loader)

`loader` 用于对模块的源代码进行转换，可以使你在「载入」模块时预处理文件。

`loader` 类似于其他构建工具中「任务(`task`)」，提供了处理前端构建步骤的方法。

`loader` 可以将文件从不同的语言（如 `TypeScript`）转换为 `JavaScript`，或将内联图像转换为 `data URL`。允许你直接在 `JavaScript` 模块中 `import CSS` 文件。

#### 示例

配置 `loader` 使 `webpack` 加载 `CSS` 文件，或者将 `TypeScript` 转为 `JavaScript`。

首先安装相对应的 `loader`：

```powershell
npm install --save-dev css-loader
npm install --save-dev ts-loader
```

然后配置 `webpack` 对每个 `.css` 使用 `css-loader`，所有 `.ts` 文件使用 `ts-loader`：

```javascript
// webpack.config.js
module.exports = {
  ...
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  }
}
```

#### 使用 loader 的三种方式

1. 配置：在 `webpack.config.js` 文件中指定 `loader`。（推荐）

前面展示过了，这里就不重复了。

2. 内联：在每个 `import` 语句中显式指定 `loader`。

可以在 `import` 语句或任何等效于 `import` 的方式中指定 `loader`。使用 `!` 将资源中的 `loader` 分开。分开的每个部分都相对于当前目录解析，示例：

```javascript
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

3. CLI：利用 `shell` 命令指定 `loader`。

```powershell
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```

#### loader 特性

* `loader` 支持链式传递。`loader` 链中每个 `loader`，都对前一个 `loader` 处理后的资源进行转换。`loader` 链会按照相反的顺序执行。第一个 `loader` 将（应用转换后的资源作为）返回结果传递给下一个 `loader`，依次这样执行下去。最终，在链中最后一个 `loader`，返回 `webpack` 所预期的 `JavaScript`。
* `loader` 可以是同步的，也可以是异步的。
* `loader` 运行在 `Node.js` 中，并且能够执行任何可能的操作。
* `loader` 接收查询参数，用于对 `loader` 传递配置。
* `loader` 也能够使用 options 对象进行配置。
* 除了使用 `package.json` 常见的 `main` 属性，还可以将普通的 `npm` 模块导出为 `loader`，做法是在 `package.json` 里定义一个 `loader` 字段。
* 插件可以为 `loader` 带来更多特性。
* `loader` 能够产生额外的任意文件。

#### 解析 loader

`loader` 遵循标准的 [模块解析][8]。多数情况下，`loader` 将从模块路径（通常将模块路径认为是 `node_modules`）解析。

`loader` 模块需要导出为一个函数，并且使用 `Node.js` 兼容的 `JavaScript` 编写。通常使用 `npm` 进行管理，但是也可以将自定义 `loader` 作为应用程序中的文件。按照约定，`loader` 通常被命名为 `xxx-loader`（例如 `json-loader`）。有关详细信息，请查看 [如何编写 loader？][9]。

### 4. 插件(plugins)

插件是 `webpack` 的支柱功能。`webpack` 自身也构建于插件系统之上。

插件目的在于解决 `loader` 无法实现的其他事。

#### 剖析

`webpack` 插件是一个具有 `apply` 方法的 `JavaScript` 对象。`apply` 属性会被 `webpack compiler` 调用，并且 `compiler` 对象可在整个编译生命周期访问。

```javascript
// ConsoleLogOnBuildWebpackPlugin.js
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
  apply(compiler) {
    // compiler hook 的 tap 方法的第一个参数，应该是驼峰式命名的插件名称。
    // 建议为此使用一个常量，以便它可以在所有 hook 中复用。
    compiler.hooks.run.tap(pluginName, compilation => {
      console.log('webpack 构建过程开始！');
    });
  }
}
// 插件编写属于比较深入的内容，这里不过多探讨，目前仅需要知道实现原理即可
```

#### 用法

由于插件可以携带参数/选项，你必须在 `webpack` 配置中，向 `plugins` 属性传入 `new ` 实例。

#### 配置写法

```javascript
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin') //通过 npm 安装
const webpack = require('webpack') //访问内置的插件
const path = require('path')

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: 'babel-loader'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
}
```

## 了解更多

### 1. 模式(mode)

对应属性：`mode | String`

作用说明：
通过将 `mode` 参数设置为 `development`, `production` 或 `none`，可以启用对应环境下 `webpack` 内置的优化。默认值为 `production`。

#### 用法

1. 在配置文件中设置

```javascript
// webpack.config.js
module.exports = {
  ...
  mode: 'production'
};
```

2. 通过 CLI 参数设置

```powershell
webpack --mode=production
```

#### 支持模式

| 选项 | 描述 |
| --- | --- |
| development | 会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。 |
| production | 会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin. |
| None | 不选用任何默认优化选项 |

#### 根据 mode 改变编译行为

```javascript
// webpack.config.js
var config = {
  entry: './app.js'
  ...
}

module.exports = (env, argv) => {

  if (argv.mode === 'development') {
    config.devtool = 'source-map';
  }

  if (argv.mode === 'production') {
    ...
  }

  return config
}
```

### 2. 模块(modules)

在模块化编程中，开发者将程序分解成离散功能块，并称之为「模块」。

每个模块具有比完整程序更小的接触面，使得校验、调试、测试轻而易举。 精心编写的「模块」提供了可靠的抽象和封装界限，使得应用程序中每个模块都具有条理清楚的设计和明确的目的。

`webpack` 将「模块」的概念应用于项目中的任何文件。

#### 什么是 webpack 模块

对比 `Node.js` 模块，`webpack` 「模块」能够以各种方式表达它们的依赖关系，几个例子如下：

样式：`(url(...))`
`ES2015`: `import`
`CommonJS`: `require()`
`HTML`: `<img src=...>`
`AMD`: `define | require`
`css/sass/less`: `@import`

#### 支持的模块类型

`webpack` 通过 `loader` 可以支持各种语言和预处理器编写模块。`loader` 描述了 `webpack` 如何处理「非 JavaScript(non-JavaScript) 模块」，并且在 `bundle` 中引入这些「依赖」。

目前 `webpack` 已经但不限于支持以下语言的 `loader`:

* [CoffeeScript][10]
* [TypeScript][11]
* [ESNext (Babel)][12]
* [Sass][13]
* [Less][14]
* [Stylus][15]


### 3. 模块解析(module resolution)

`resolver` 是一个库，用于帮助找到模块的绝对路径。 它帮助 `webpack` 从每个如 `require/import` 语句中，找到需要引入到 `bundle` 中的模块代码。 当打包模块时，`webpack` 使用 `enhanced-resolve` 来解析文件路径。

#### webpack 中的解析规则

使用 `enhanced-resolve`，`webpack` 能够解析三种文件路径：

##### 1. 绝对路径

```javascript
// 已经取得文件的绝对路径，因此不需要进一步再做解析。
import '/home/me/file';
import 'C:\\Users\\me\\file';
```

##### 2. 相对路径

```javascript
// 在这种情况下，使用 import 或 require 的资源文件所在的目录，被认为是上下文目录。
// 在 import/require 中给定的相对路径，会拼接此上下文路径，以产生模块的绝对路径。
import '../src/file1';
import './file2';
```

##### 3. 模块路径

```javascript
import 'module';
import 'module/lib/file';
// 解释很啰嗦，感兴趣可以自己去看一下文档
```

#### 缓存

每次文件系统访问都会被缓存，以便更快触发对同一文件的多个并行或串行请求。在 观察模式下，只有修改过的文件会从缓存中摘出。如果关闭观察模式，会在每次编译前清理缓存。

### 4. 依赖图(dependency graph)

任何时候，一个文件依赖于另一个文件，`webpack` 就把此视为文件之间有「依赖关系」。这使得 `webpack` 可以接收非代码资源（例如 `images` 或 `web fonts`），并且可以把它们作为「依赖」提供给你的应用程序。

`webpack` 从命令行或配置文件中定义的「入口」开始，递归地构建一个依赖图，这个依赖图包含着应用程序所需的每个模块，然后将所有这些模块打包为少量可由浏览器加载的 `bundle`（通常只有一个）。

### 5. 浏览器兼容性

`webpack` 支持所有 `ES5` 兼容（`IE8` 及以下不提供支持）的浏览器。`webpack` 的 `import()` 和 `require.ensure()` 需要环境中有 `Promise`。如果你想要支持旧版本浏览器，你应该在使用这些 `webpack` 提供的表达式之前，先 [加载一个 polyfill][2]。

## 总结

通过整理这篇文档我已经对 `webpack` 有了一个初步的认识和了解了。

当然如果你要真正的在项目中投入使用 `webpack` 仅仅阅读这一篇文章是不够的，你还需要去深入地阅读了解文档里的各种配置参数和其他常用的前端构建工具或预处理器配合 `webpack` 进行调试使用。

前路漫漫，与君共勉。

## 参考
* [webpack 中文文档][4]
* [掘金：Webpack中publicPath详解-Mello_Z][5]
* [segmentfault：webpack output.publicPath 如何动态配置地址][6]
* [GitHub: iview-admin 源码][7]

[1]: https://webpack.docschina.org/concepts/
[2]: https://webpack.docschina.org/guides/shimming/
[3]: https://webpack.docschina.org/configuration/output#output-filename
[4]: https://webpack.docschina.org/
[5]: https://juejin.im/post/5ae9ae5e518825672f19b094
[6]: https://segmentfault.com/q/1010000008980858
[7]: https://github.com/iview/iview-admin
[8]: https://webpack.docschina.org/concepts/module-resolution/
[9]: https://webpack.docschina.org/development/how-to-write-a-loader
[10]: http://coffeescript.org/
[11]: https://www.typescriptlang.org/
[12]: https://babeljs.io/
[13]: http://sass-lang.com/
[14]: http://lesscss.org/
[15]: http://stylus-lang.com/
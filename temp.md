### 初步分析

使用 webpack 内置的 stats 分析，stats 是 webpack 整个构建过程中生成的统计信息，包括构建总时长、每一个资源的大小等等。

使用以下命令就可以生成 stats 文件

> webpack --env production --json > stats.json

生成的结果大概是这样的

```json
{
  "errors": [],
  "warnings": [
    "configuration\nThe 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.\nYou can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/"
  ],
  "modules": [],
  "filteredModules": 0,
  "logging": {
    "webpack.buildChunkGraph.visitModules": {
      "entries": [],
      "filteredEntries": 2,
      "debug": false
    }
  }
}
```

modules 里面就是生成的模块的信息。

有了这个文件我们可以通过[Webpack Chart](https://alexkuz.github.io/webpack-chart/)来使用图形状展示这个东西

这是一种分析方式但是不太完美，来看一下它的缺点：

- 颗粒度太粗，看不出问题所在，只能得出最终的结论。

于是我们衍生出两个问题：

1. 构建时长，究竟是哪个 loader 或者 plugin 的时间长，怎么得到每一个阶段的构建时长？
2. 构建资源大，究竟为什么这么大？

### 速度分析

根据上面提出的两个问题，我们来解决第一个问题，社区已经有完善的插件`speed-measure-webpack-plugin`。它的作用的能够分析出每一个 loader 或者 plugin 的构建时长，颗粒度细。

安装

> npm install speed-measure-webpack-plugin --save-dev

使用

```js
const SpeedMeasureWebpackPlugin = require('speed-measure-webpack-plugin')
const smp = new SpeedMeasureWebpackPlugin()

module.exports = smp.wrap({
  //webpack 配置
})
```

打包之后的结果：
![](https://raw.githubusercontent.com/hubvue/webpack-learn/master/image/webpack-analyzer-speed.jpeg)

### 体积分析

根据第二个问题，打包出来的内容我们是知道一个 chunk 的大小的，但是为什么这么大，里面是什么样子，我们完全不知道。解决这个问题，社区也有一个插件可以帮助分析`webpack-bundle-analyzer`

安装

> npm install webpack-bundle-analyzer --save-dev

使用

```js
const BundleAnalyzer = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
module.exports = {
  plugins: [new BundleAnalyzer()]
}
```

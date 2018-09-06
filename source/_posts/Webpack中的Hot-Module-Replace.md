title: Webpack 中的 Hot-Module-Replace
date: 2018-09-06 23:37:53
categories: 技术
tags:

- 技术
- 总结

---

现在前端都是工程化开发，很多成熟的脚手架来帮助我们省掉搭建工程的时间，比如 React Starter Kit，Create-react-app，DVA 等等。
但是一些基本知识我们还是需要知道的。

今天就记录一下 Webpack 在开发过程中经常用到的热加载模块简称 HMR，因为在 webpack 看来，一切皆模块，所以这个模块替换，对它来说是很容易做到的

### 如何在开发中开启热替换呢

### - 使用 webpack-dev-server

这种方式简单

```
npm install --save-dev webpack-dev-server webpack-cli // 先安装相关依赖

// webpack.conf.js
...
devServer: {
  port: 3000,
  hot: true, // 启用 webpack 的模块热替换特性
  historyApiFallback: true, // 任意的 404 响应可以提供为 index.html 页面
}
```

然后通过命令*webpack-dev-server --config webpack.conf.js*来启动即可

### - 使用 express，webpack-dev-middleware，webpack-hot-middleware

一般这种情况多数用在需要使用 express 来提供 API 的情况

```
npm install --save-dev express webpack webpack-dev-middleware webpack-hot-middleware // 安装依赖
// devServer.js
const express = require('express')
const webpack = require('webpack')
const webpackConfig = require('./webpack.dev.conf')
const compiler = webpack(webpackConfig)
const app = express()
app.use(
  devMiddleware(compiler, {
    // webpack-dev-middleware options
    writeToDisk: true, //把构建出来的文件写入磁盘
    noInfo: true,
    publicPath: webpackConfig.output.publicPath,
  }),
)
app.use(hotMiddleware(compiler))
app.use('/', express.static(process.cwd() + `${conf.paths.public}`))
app.get('*', function(request, response) {
  response.sendFile(path.resolve(process.cwd(), `${conf.paths.public}`, 'index.html')) // 所有请求返回index.html， 等同于上面webpack-dev-server中的historyApiFallback
})
app.listen(3000, () => console.log('app listening on port 3000!'))
...

// webpack.dev.conf.js
module.exports = {
  ...
  entry: ['webpack-hot-middleware/client', `index.js`], // 需要引入middleware中的client
  ...
}
```

就如同上面说的那样，常用的两种 HMR 方式，其实第一种更简单。

这里还需要说一个 React Hot Loader，它的作用则是增强了 webpack 中的 hot 功能，使得在 replace 之后，react component 中的状态也能够保留，在上面的两种方式的基础上，额外配置：

```
npm install --save-dev react-hot-loader // 安装依赖
// .babelrc
{
  "plugins": ["react-hot-loader/babel"]
}
// webpack.conf.js
module.exports = {
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    ...
  ]
}
// entry point
if(module.hot){
  module.hot.accept()
}
```

使用 HMR 的功能的时候，如果同时也使用了 Redux 的话，则需要注意，需要重新 replace 的 component 不能创建新的 store 不然就会出现下面的错误

> warning.js:10 <Provider> does not support changing `store` on the fly. It is most likely that you see this error because you updated to Redux 2.x and React Redux 2.x which no longer hot reload reducers automatically. See https://github.com/reactjs/react-redux/releases/tag/v2.0.0 for the migration instructions.

即不能对含有 Provider 的组件进行 replace，最佳实践应该是对 Router 或者 Router 的上层组件来进行热加载

具体的详细配置可以参考[例子](https://blog.csdn.net/dengdengda/article/details/78746208)或者[官网](https://www.npmjs.com/package/react-hot-loader)

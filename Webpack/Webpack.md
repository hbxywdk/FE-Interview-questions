#### 简述 webpack 运行原理
##### 基本概念
- Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
- Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
- Chunk：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
- Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
- Plugin：扩展插件，在 Webpack 构建流程中的特定时机会广播出对应的事件，插件可以监听这些事件的发生，在特定时机做对应的事情。

##### 流程概括
Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：
1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译；
3. 确定入口：根据配置中的 entry 找出所有的入口文件；
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
5. 完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

> 以上内容摘自《深入浅出Webpack》


#### 什么是 Tree Shaking？
Tree Shaking 可以用来移除没用用到过的四代码，要使用它则必须使用 ES6 的模块语法。


#### 如何写 webpack 的 loader
```
// 最简单的Loader：
module.exports= function(source) {
    // source 是 compiler 传递给 loader 的一个文件的原内容。
    // 通常在 loader 中需要做相关处理，之后将转换后的内容返回出去。 
    return source
}
```

#### 项目打包的 source map有什么用？
项目打包出来的代码都是压缩的，一旦出错，很难定位。
source map 记录了代码压缩前后的一些信息，使得我们可以调试压缩后的代码。
补充：一般只在开发模式中打开。source map 会增加项目的打包时间。


#### 怎么理解webpack？说说它与gulp区别。
Webpack是前端构建工具，实现了模块化开发和文件处理，对Webpack来说所有文件都是模块。
Gulp是一种流程工具，会执行用户定义好的流程。


#### 用过哪些webpack插件？
- CleanWebpackPlugin：清理指定目录的文件
- DefinePlugin：定义全局变量
- CopyWebpackPlugin：复制文件用的
- HtmlWebpackPlugin：生成HTML文件的
- DllPlugin：提升构建速度 https://blog.csdn.net/chiuwingyan/article/details/81155281
- ExtractTextPlugin：单独抽离出css文件
- UglifyJsPlugin：压缩Js的
- CommonsChunkPlugin：单独打包某些Js文件

Webpack更新到4.X版本后很多插件发生了变化：
- 比如引入了mode
```
module.exports = {
  mode: 'development' // development || production
}
```
生产模式下就会自动压缩代码，UglifyJsPlugin也就显得没那么必要了。

- 使用optimization.splitChunks替代了CommonsChunkPlugin
- 还有很多就不列了


#### 如何优化 webpack 打包出来的文件？
- 使用 CommonsChunkPlugin（webpack4之前）单独抽取各个类库，避免所有类库打包再一起，造成加载过久。
- 单独抽离 Css 文件
- 压缩 Css、Js 等各种文件
- 开启 gzip 压缩（需要服务端同时支持），它能将你已经压缩过的代码再压缩一半以上！
使用 CompressionWebpackPlugin 插件配置 gzip 压缩，所有的文件都会被打包为两份，一份是正常的文件，一份是.gz后缀的压缩文件，对于支持 gzip 的浏览器，服务端就会返回 .gz 文件。


#### webpack如何优化编译速度
##### 最简单同时也是收益最大的方法就是将使用到的类库以CDN的形式引入
```
module.exports = {
    externals: {
        // 引用名：类库导出的全局名
        "vue": "Vue"
    },
 }
```
然后再HTML文件中以 script 引入对应 CDN 即可。

当然，也有 webpack 插件可以实现同样功能，可自行搜索。

##### 使用 DllPlugin 配合 DllReferencePlugin 
这种方式其实和第一种引入 CDN 效果上差不多，不过这种对于没有 CDN 的库也可以用。
- 首先需要额外创建一个 webpack.dll.config.js
```
const path = require('path');
const webpack = require('webpack');
module.exports = {
    entry: {
        vendor: ['react', 'react-router-dom'] // 将用到的插件放到到vendor中
    },
    output: {
        path:path.resolve('./dist'), // 输出位置
        filename: '[name].dll.js', // 输出名
        library: '[name]_library' // 需与下面 name: '[name]_library' 一样
    },
    plugins:[
        new webpack.DllPlugin({
            path: path.resolve('./dist','[name]-manifest.json'),
            name: '[name]_library'
        })
    ]
}
```
- 单独配置一行 npm script 脚本，并运行。
此脚本会生成两个文件：vendor-manifest.json 与 vendor.dll.js
```
"scripts": {
    "dll": "webpack -p --config webpack.dll.config.js --progress --profile --colors"
  }
```
- 在 webpack.config.js 中使用 DllReferencePlugin 插件：
```
plugins: [
    new webpack.DllReferencePlugin({
        manifest: require('./dll/vendor-manifest.json')
    })
]
```
- 最后再HTML中引入 vendor.dll.js （注意要在 bundle.js 前引入）
```
<script src="/vendor.dll.js"></script>
```
- 这样每次打包的时候 webpack 就不会打包第三方类库了。不过这样的一个缺点是，当我们添加了某个库，就必须重新修改 webpack.dll.config.js 文件与运行 npm script

##### 优化构建时的搜索路径
在配置 loader 时，可以增加 include、exclude 配置。

include 用来精确搜索路径与排除某些路径。

exclude 用于忽略某些路径，比如配置忽略node_modules文件夹：exclude: /node_modules/，这样 webpack 就不会去遍历这个文件夹内的内容，以优化构建时间。

##### 使用 require.ensure 按需加载

##### 尽量在写代码的时候就不要产生不必要的冗余代码


#### webpack是吧代码全部吐给插件，还是一段段的吐？
可以说是全部，插件开发者可以根据需求自己去取想要的代码。
   

#### 如何写 webpack 的 plugin？想要写一个插件替换代码中的某一段怎么写？
```
class MyPlugin {
  constructor(){
  }

  apply(compiler){
    // console.log('COMPILER', compiler)
    compiler.plugin('compilation', function(compilation) {
      // console.log('compilation', compilation)
    })
    
    compiler.plugin('emit', (compilation, callback) => {
      // 遍历所有资源文件
      for (var filePathName in compilation.assets) {
        // 获取文件内容
        let content = compilation.assets[filePathName].source() || ''

        if(typeof content === 'string') {
          // 要匹配内容的正则
          const reg = /Welcome/ig
          // 替换
          content = (content && content.replace) && content.replace(reg, 'Test')
          // 返回重写后的内容
          compilation.assets[filePathName] = {
            source () {
              return content;
            },
            size () {
              return content.length;
            }
          }
        }

      }
      callback()
    })
    
  }
}
```
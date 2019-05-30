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
---
title: 应用-webpack
date: 2025/04/01
tags:
 - js
 - webpack
categories:
 - 应用
 - webpack
---

# webpack


### process.env

#### process.env的本质
process.env是Node.js中的一个对象，包含当前进程的所有环境变量。例如：
```js
console.log(process.env); // 输出当前进程的所有环境变量
```
而NODE_ENV不是process.env的默认属性。它是用户自定义的环境变量，用于区分开发、测试、生产等环境。

#### 设置NODE_ENV的方法

1. 通过命令行设置（临时生效）
   
```bash
# Unix/Linux/macOS  
export NODE_ENV=production  
node app.js
```
 
2. Windows（使用 cross-env 跨平台兼容）
   
```bash
npx cross-env NODE_ENV=production node app.js
```
3. 通过.env文件设置（持久化配置）：
   
```bash
#.env 文件 
NODE_ENV=production
```
在代码中加载.env文件：
```js
require('dotenv').config(); // 使用 dotenv 库加载环境变量  
console.log(process.env.NODE_ENV); // 输出: "production"
```
4. **NODE_ENV的典型用途**

package.json
```json
{
 
  "scripts": {
    "dev": "cross-env NODE_ENV=development OTHER_CONTENT=XXXXX 其他命令"
 }
}
```

:::warning 不同环境

如果不使用插件cross-env，不同的环境配置写法不一致
```json
"scripts": {
    "dev-mac": "export NODE_ENV=development && node dev.js",
    "dev-win":  "set NODE_ENV=development && node dev.js"
}
```
:::

[参考链接](/blogs/plugin/01plugin.html#通用类插件库)

构建工具配置：如Webpack、Vite等根据NODE_ENV决定构建模式（开发/生产）。
代码逻辑分支：
```js
if (process.env.NODE_ENV === 'production') {  
  // 生产环境逻辑  
} else {  
  // 开发环境逻辑  
}
```

## webpack插件

### DefinePlugin（内置）
这个插件允许在构建过程中定义全局常量，这些常量可以在代码中被访问。由于Webpack在构建时处理这些常量，它们的值必须是`字符串`形式，以便正确替换。

#### 应用场景

1. html中设置值
   
```html
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```

DefinePlugin允许在编译时创建配置的全局常量

```js
const path = require("path");
const { DefinePlugin } = require("webpack");
module.exports = {
  mode: "development",
  plugins: [
    new DefinePlugin({
      BASE_URL: "'./'",
	    __VUE_OPTIONS_API__: true, //vue3的配置，关闭option api，默认是false
    })
  ]
};

```
这个时候，编译template就可以正确的编译了，会读取到BASE_URL的值；

2. 在不同的环境可以通过process.env上注入一些可供使用的数据

```bash
cross-env NODE_ENV=development OTHER_CONTENT=XXXXX 其他命令
```
NODE_EN 和 OTHER_CONTENT就会在process.env上获取

```js
const settingData = Object.keys(obj).reduce((acc, key) => {
  acc[`PROCESS_ENV_${key.toUpperCase()}`] = JSON.stringify(obj[key]);
  return acc;
}, {});
// 使用示例：
// new webpack.DefinePlugin(settingData)
```

如果 Webpack 版本 >= 5，直接使用：

```js
import { Configuration, DefinePlugin } from 'webpack';
```
若版本 < 5，可能需要：

```js
const webpack = require('webpack');
const { DefinePlugin } = webpack;
```


对于动态值，建议使用 dotenv-webpack 或 Webpack 的 EnvironmentPlugin：
```js
import { EnvironmentPlugin } from 'webpack';

// webpack.config.ts
plugins: [
  new EnvironmentPlugin({
    API_URL: process.env.API_URL,
  }),
]
```

### EnvironmentPlugin（内置）

EnvironmentPlugin是使用DefinePlugin键的简写process.env.

接受EnvironmentPlugin一个键`数组`或一个将其键映射到其默认值的`对象`。
```js
new webpack.EnvironmentPlugin(['NODE_ENV', 'DEBUG']);
```
这相当于以下DefinePlugin应用程序：
```js
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
  'process.env.DEBUG': JSON.stringify(process.env.DEBUG),
});
```
::: warning 警告
EnvironmentPlugin检查process.env指定的变量。如果缺少，它会搜索配置中提供的默认值。如果环境变量和默认值均未定义，则会抛出错误：“ EnvironmentPlugin-${key}环境变量是undefined”。
:::

### HtmlWebpackPlugin

该插件将生成一个 HTML5 文件，该文件使用script标签在正文中包含所有 webpack 包。按如下方式将插件添加到 webpack 配置中：

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');

module.exports = {
  entry: 'index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'index_bundle.js',
  },
  plugins: [new HtmlWebpackPlugin()],
};
```
这将生成一个dist/index.html包含以下内容的文件：
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
  </body>
</html>
```

如果有多个 webpack 入口点，它们都将包含`<script>`在生成的 HTML 中的标签中。

如果 webpack 的输出中有任何 CSS 资源（例如，使用MiniCssExtractPlugin提取的 CSS ），那么这些资源将包含在生成的 HTML 元素`<link>`中的标签中`<head>`。

[webpack](https://bqq9knyjcuo.feishu.cn/docx/VtaxdlwLVoGNUexHiSYcSS4MnTd?from=from_copylink)

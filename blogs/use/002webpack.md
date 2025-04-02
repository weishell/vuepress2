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

## webpack


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

:::

构建工具配置：如Webpack、Vite等根据NODE_ENV决定构建模式（开发/生产）。
代码逻辑分支：
```js
if (process.env.NODE_ENV === 'production') {  
  // 生产环境逻辑  
} else {  
  // 开发环境逻辑  
}
```
[webpack](https://bqq9knyjcuo.feishu.cn/docx/VtaxdlwLVoGNUexHiSYcSS4MnTd?from=from_copylink)

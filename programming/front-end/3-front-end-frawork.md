# 3 前端框架

## Yarn

Yarn是一个软件包管理器，代码通过 软件包（package） 的方式被共享。一个软件包里包含了所有需要共享的代码，以及一个描述软件包信息的文件 package.json （叫做 清单）。

### 文档

[https://www.yarnpkg.cn/cli/add](https://www.yarnpkg.cn/cli/add)

### 常用命令

```bash
npm install -g yarn

yarn --version
# 安装依赖
yarn install
# 编译
yarn build (或者yarn run build)
# 修复文件
yarn run lint
# 开发模式运行
yarn run <scriptName> ...
yarn run server
```

## NodeJS

Node.js 是一个开源与跨平台的 JavaScript 运行时环境，简单的说Node.js 就是运行在服务端的JavaScript。

### 常用命令

```bash
node -v
node helloworld.js
```

### npm

NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题。

例如，用Nodejs创建一个web应用，示例：[https://www.runoob.com/nodejs/nodejs-http-server.html](https://www.runoob.com/nodejs/nodejs-http-server.html)

```bash
npm -v
# 安装模块
$ npm install <Module Name>
# 升级，-g代表全局安装
npm install npm -g
# 查看模块
npm ls
# 升级模块
npm update express
# 发布模块
npm publish
```


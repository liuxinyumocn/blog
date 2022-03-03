# NPM笔记

------------------------------------------

## 初始化项目

构建一个标准的npm模块

```shell
npm init
```

## 安装

```shell
npm install <模块名>
```

模块安装分为本地与全局

```shell
npm install koa -g
```

**本地安装：**

第三方模块将被安装到当前目录的 node_modules 下

通过 **require('模块名')** 即可导入本地模块

**全局安装：**

第三方的模块一般被安装到 /usr/local/lib/node_modules 目录或 Node.js 目录

可以直接在命令行使用

不可以使用 require('模块名') 来引用

## 生产依赖和开发依赖

**开发依赖：**

使用 --save-dev 选项即可将软件包安装位开发依赖，依赖信息将被保存到 package.json 的 devDependencies 中。

```shell
npm install eslint --save-dev
```

**生产依赖：**

使用 --save 选项即可将软件包安装位生产依赖，开发环境和生产环境都需要使用，依赖细腻保存在 package.json 的 dependencies 中。

```shell
npm install koa --save
```


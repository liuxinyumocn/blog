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

## LINK与UNLINK

将本地项目包软链至项目工程中，比如自己开发了插件 my-plugin ，在 my-project 项目中使用，需要在 my-project 项目中调试 my-plugin 时，使用到该能力。

```shell
#进入到 my-plugin package.json 对应目录
cd .../my-plugin
#可以理解为复制粘贴中的“复制”
npm link

#前往 my-project package.json 对应目录
cd .../my-project
#link my-plugin package.json 中声明的插件名称（不是目录名字）
npm link my-plugin

#解除时先在 project 目录卸载 my-plugin 的软链
npm unlink my-plugin
#再回到 plugin 目录卸载全局软链
npm unlink
```


# Node.js中module与exports

## 声明

Node.js应用由模块组成，采用**CommonJS**模块规范，每一个JS文件就是一个模块，拥有独立的作用域，如果希望将模块供其他的文件（模块）使用，则需要进行对外声明。

**module** 是一个全局对象，**module.exports** 即时对外的声明接口，其他模块加载当前模块时，读取的即是该接口声明的内容。

我们挂载的方式是：

```javascript
function a(b){
    return b;
}
module.exports.a = a;
```

在Node.js中，实际上为了方便实用，可以使用 **exports** 直接代替 **module.exports** 进行对外声明，但需要注意的是，如果使用 exports 对外声明时，必须采用属性赋值的方式，即：

```javascript
exports.a = 'hello Mr.Liu'
```

不可以直接赋值，例如：

```javascript
exports = 'hello Mr.Liu' //无法导出
```

原因很简单，看下下面的例子：

```javascript
let a = module.exports;
a = 'hello Mr.Liu';		// a 仅是一个引用，直接赋值将覆盖其引用
```

## 加载

其他文件需要加载模块时，使用 **require** 关键词，例如：

```javascript
const math = require('./math');
```

常见的规则：

- 可以忽略 .js 文件后缀
- 参数以 ‘/’ 开头，将加载一个绝对路径文件
- 参数以 ‘./’ 开头，将加载一个基于当前目录的相对路径文件
- 如果不是上述两个开头，将加载核心模块，或者当前工作区中 **node_modules** 下的模块
- 如果上述规则没找到，会找 .js .json .node 后缀的文件模块
- 如果是个目录，则先找目录内 package.json 文件，根据配置找main入口，如果还没有这个文件，则加载 index.js 或 index.node
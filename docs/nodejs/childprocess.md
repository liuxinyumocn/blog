# Node.js多进程

## 创建子进程

​    面对单进程单线程对多核使用不足的问题，普遍的经验是启动多进程。理想状态下每个进程各自利用一个CPU，以此实现多核CPU的利用。Node 提供了 child_process 模块。

创建子进程：

- spawn()：启动一个子进程来执行命令。
- exec()：启动一个子进程来执行命令，与 spawn() 不同的是其接口不同，它有一个回调函数获知子进程的状况。
- execFile()：启动一个子进程来执行可执行文件。
- fork()：与 spawn() 类似，不通电在于他创建 Node 的子进程只需指定要执行的 JavaScript 文件模块即可。

spawn()与exec()、execFile()不同的是，后两者创建时可以指定timeout属性设置超时时间，一旦创建的进程运行超过设定的时间将被杀死。

以运行 **node worker.js** 例子说明上述的功能使用区别：

```javascript
const cp = require('child_process');
cp.spawn('node',['worker.js']);
cp.exec('node worker.js',function(err,stdout,stderr){	//如同 命令行运行
    //some codes
})
cp.execFile('worker.js',function(err.stdout,stderr){	//注释1
    //some codes
})
cp.fork('./worker.js');	//对于 js 脚本的快速子进程创建
```

注释：

1. 此处文件必须是可执行文件，如 Windows 操作系统下的 .exe 后缀文件，若为 JavaScript 脚本文件，其文件首行必须添加如下代码：

```javascript
#!/usr/bin/env node
```

四种方法的差别：

| 类型       | 回调/异常 | 进程类型 | 执行类型   | 可设置超时 |
| ---------- | --------- | -------- | ---------- | ---------- |
| spawn()    | ×         | 任意     | 命令       | ×          |
| exec()     | √         | 任意     | 命令       | √          |
| execFile() | √         | 任意     | 可执行文件 | √          |
| fork()     | ×         | Node     | JavaScript | ×          |

​    尽管4种方法创建子进程有些差别，但实际上后面3种都是对spawn()的延伸应用。

## 进程间通信

​    在Master-Worker模式中，要实现主进程管理和调度工作进程的功能，需要主进程和工作进程之间进行通信，对于child_process模块创建好的子进程而言，父子进程之间通信是十分容易的。
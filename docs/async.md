# 异步编程风格笔记

JavaScript开发经常用异步，但是可能只是会使用，一知半解，做一个笔记全面了解一下。

## Promise

该对象用于表示一个异步操作的最终完成（或失败）及其结果值，一个Promise有以下3种状态：

- pending：初始状态
- fulfilled：操作成功
- rejected：操作失败

Promise 只会从 pending 转换为 fulfilled 或者 rejected，且只会转换1次。

常见的一种使用：

```javascript
const fs = require('fs');
function readFileAsync(path){
    return new Promise(function(resolve,reject) {
        fs.readFile(path,function(err,data){
            if(err){
                reject(err);
                return;
            }
            resolve(data)
        });
    });
}

readFileAsync('xxxx/data.txt')
	.then(function(data) {
    	console.log(data)
	})
	.catch(function(err) {
    	console.error(err)
	});
```

这里记录一个比较好用的链式调用：

Promise 的 then 与 catch 回调函数的返回值会作为下一个 then/catch 的输入参数，因此可以通过链式 Promise 来扁平化嵌套的回调函数。

```javascript
//续上面的代码块
//顺序读取两个文件的数据
readFileAsync('xxxx/data_01.txt')
	.then(function(data) {
    	console.log('data_01 成功',data)
		return readFileAsync('xxxx/data_02.txt');
	})
	.then(function(data) {
    	console.log('data_02 成功',data)
		return readFileAsync('xxxx/data_02.txt');
	})
	.catch(function(err) {
    	console.log('读取失败',err)
	});

```



## async & await

这两个关键词组合起来使用是对 Promise 的一种便捷调用方法。async 关键词用于修饰一个函数的声明，例如：

```javascript
async function fn0(){
    //others...
}

//或者
const fn1 = async ()=>{
    //others...
}
```

被 async 修饰的函数体内（花括号内）才可以使用 await 关键词，await 的关键词后将接收一个 Promise 实例对象，此时 await 处代码将处于停滞状态，等待该 Promise 实例的状态推进，即触发 resolve 或者 reject 的回调，对于 resolve 回调将直接成为 await 处代码的返回值，对于 reject 的回调需要使用 try{ ... }catch(e){ ... } 进行捕获，其中 catch(e) 的形参 e 即为 reject 的回调参数：

```javascript
const fs = require('fs');
function readFileAsync(path){
    return new Promise(function(resolve,reject) {
        fs.readFile(path,function(err,data){
            console.log('顺序2')
            if(err){
                reject(err);
                return;
            }
            resolve(data)
        });
    });
}

async function fn0(){
    console.log('顺序1');
    try{
        const f = await readFileAsync('xxxx.txt');
        //f 为 resolve 的返回值
    }catch(e){
        //e 为 reject 的返回值
    }
    console.log('顺序3');
    /*
    	控制台将顺次打印 顺序1 顺序2 顺序3	
    */
}

```

如果 await 关键词后并不是 Promise 实例对象，则无视 await 关键词修饰，变成一种常规的同步执行方式。


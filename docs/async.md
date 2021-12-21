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


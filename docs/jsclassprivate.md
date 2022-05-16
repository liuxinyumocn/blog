# JS私有成员变量

总结一下JS定义私有成员变量的方法

### 方法一：下划线区分

这个方法属于约定，并不强制，因此不是严格意义上的私有。

```js
class Dog {
    constructor(){
        this._name = 'LaiFu';
        this._age = '';
    }
	getInfo(){
    	return 'name:'+this._name+' age:'+this._age;
    }    
}
```

### 方法二：Proxy包装

```js
class Dog {
    constructor(){
        this._name = 'LaiFu';
        this._age = '3';
    }
	getInfo(){
    	return 'name:'+this._name+' age:'+this._age;
    }    
}
const dog = new Dog();
//对 dog 实例添加代理
const handler = {
    get(target, prop){
        if(prop.startsWith('_')) {		//过滤掉 _ 开头的属性
            return;
        }
        //如果是访问函数则需要bind原始的Object
        //否则成员函数将读不到 this._xxxx 因为函数访问时也需要走Proxy
        if(typeof target[prop] === 'function'){
            return target[prop].bind(target);
        }
        return target[prop];
    },
    set(target,prop,value){
        if(prop.startsWith('_')){
            return;
        }
        target[prop] = value;
    },

    ownKeys(target,prop){	//重写 ownKeys 方法，使得 Object.keys(ob) 也读不到
        return Object.keys(target).filter(key => !key.startsWith('_'))
    }

}

const proxy = new Proxy(dog,handler);
for (const key of Object.keys(proxy)){
    console.log(key, proxy[key]);	// 无打印 因为没有额外属性
}
console.log(proxy.getInfo())	//name:LaiFu age:3
```

很明显这个方法使用起来很笨重。

### 方法三：Symbol关键词

Symbol关键词定义唯一不可变的值，这就可以用于定义私有的变量

```js
const nameSymbol = Symbol('name');
const ageSymbol = Symbol('age');
class Dog {
    constructor(){
        this[nameSymbol] = 'LaiFu';
        this[ageSymbol] = '3';
    }
	getInfo(){
    	return 'name:'+this[nameSymbol]+' age:'+this[ageSymbol];
    }    
}
const dog = new Dog();
//但是 Symbol 并不是完全取不到 如下方法仍然可以获取到
console.log(Object.getOwnPropertySymbols(dog));
```



### 方法四：WeakMap存储

WeakMap的 Key 必须是指针，且当该指针无效后，会自动释放，不会引发内存泄漏。

```js
const nameWeakMap = new WeakMap();
const ageWeakMap = new WeakMap();

const setPrivateAttr = function(receiver,wmap,value){
    wmap.set(receiver,value);
}
const getPrivateAttr = function(receiver,wmap){
    return wmap.get(receiver);
}

class Dog {
    constructor(){
        nameWeakMap.set(this, void 0);
        ageWeakMap.set(this, void 0);
        
        setPrivateAttr(this,nameWeakMap,'Lai Fu');
        setPrivateAttr(this,ageWeakMap,3);       
    }
	getInfo(){
    	return 'name:'+getPrivateAttr(this,nameWeakMap)+' age:'+getPrivateAttr(this,ageWeakMap);
    }    
}
```

### 方法五：#开头写法

实际上该方法是语法糖，本质编译器还是使用了WeakMap方式底层实现。

```javascript
class Dog {
    #name;			//使用 # 声明
    #age;
    constructor(){
        this.#name = 'Lai Fu';
        this.#age = 3;
    }
	getInfo(){
    	return 'name:'+this.#name+' age:'+this.#age;
    }    
}
```

### 补充说明TS

TypeScript中支持声明 private 关键词，但这仅仅是开发时不允许访问，实际编译运行时并不是真正意义上的私有。
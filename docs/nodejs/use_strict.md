# JavaScript严格模式（use strict）

## 说明

"use strict" 指令在 JavaScript 1.8.5 (ECMAScript5) 中新增。

它不是一条语句，但是是一个字面量表达式，在 JavaScript 旧版本中会被忽略。

"use strict" 的目的是指定代码在严格条件下执行。

严格模式下你不能使用未声明的变量。

```javascript
"use strict";
x = 3.14;       // 报错 (x 未定义)
```

在函数内部声明是局部作用域（只在函数内部使用严格模式）

```javascript
x = 3.14;       // 不报错
myFunction();

function myFunction() {
	"use strict";
	y = 3.14;   // 报错 (y 未定义)
}
```

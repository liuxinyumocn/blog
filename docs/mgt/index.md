# MPT完全开发文档

## 简介

​		微信小游戏插件开发模板（WeChat Minigame Plugin Template, 以下简称 MPT）是基于 eevee-cli 构建的微信小游戏插件开发模板，提供了标准的插件开发目录结构、编译环境以及用于插件开发时的基本调试组件环境。值得注意的是本模板为小型框架，旨在方便小游戏插件开发者实现相关的功能的快速调试工作，不作为应用级别使用。



## 目录结构



```
|-- minigame_plugin
    |-- package.json
    |-- webpack.config.js
    |-- build												//构建目录
    |-- release											//调试目录
    |   |-- project.config.json
    |   |-- cloudfunctions
    |   |-- doc
    |   |-- minigame								//插件调试简易小游戏目录
    |   |-- plugin
    |-- src													//插件开发目录
```


## 开始







## 插件调试简易小游戏使用指南

​		小游戏插件开发一般以服务其他小游戏为目的，因此在插件开发的过程中 MPT 在提供了一个基于 Pixi.js 构建的简易“小游戏”工程用于模拟小游戏运行场景以及方便调试插件功能，简易小游戏给出了一些常见UI组件方便插件内各项函数功能的交互。

​		MPT的简易小游戏是由 Pixi.js 动画引擎实现的，出于对调试工作的适应做出的二次封装，而对于有更高要求的调试工作可以基于 Pixi.js 深入改装，同时也可以将需求提至MPT模板的维护人员根据常用性扩展MPT的通用能力`联系人 nebulaliu(刘昕禹)`。

### 简易小游戏目录

```
minigame
	game.js 	//小游戏启动入口
	index.js	//GUI快速定义入口
	/lib			//框架库
	/source		//媒体资源目录
	/application	//复杂调试环境入口
		scene.default.js	//默认场景定义
```

​		通常情况下，插件开发者仅需要访问 `/minigame/index.js` 文件，在 onload() 代码块内进行如下的定义工作即可（具体的组件类可参阅下文组件介绍）：

```javascript
import * from 'MPTComponents';
import MPTBase from 'lib/xxxxxx';

export default class Index extends MPTBase {
  
	onload(scene){
    
		const myPlugin = requirePlugin('myplugin')		//加载你的插件
    
		scene.add(new Button({		//其他组件类名可查阅组件介绍部分
			name:'执行插件功能1',
			on: res => {		//组件事件
				myPlugin.func1();
			}
		}));
    
		scene.add(new Button({
			name:'执行插件功能2',
			on: res => {
				myPlugin.func2();
			}
		}));
    
	}
}
```

​		对于场景高级的构建需求需要访问 `/minigame/application` 目录下开始创建定义。

### 布局策略

​		本模板本身是以简易构造、快速调试为目的，在布局能力上提供了2种基础布局能力，分别是 H布局（horizontal） 与 V布局（vertical）。

- H布局

  组件将从左往右布局（不会换行）

  支持锚点的快速设定，如左设定、中心设定与右设定。

- V布局

  组件将从上往下布局

  支持锚点的快速设定，如上设定、中心设定与下设定。



布局案例：

对于经典的 Label + Input 的这种组合，使用 h 布局后依次定义 Label 与 Input，则就完成了 左边是 Label 随后 Input 的 UI 布局。

布局组件支持相互之间的嵌套，例如上面的 H(Label + Input) 组合可以被放入 V布局内，以此来实现多个 Label + Input 组合上下顺次排列。

其中通过设定锚点位置可以确定对齐方式。

（此处将来会补图）

```javascript
import * from 'MPTComponents';
import MPTBase from 'lib/xxxxxx';

export default class Index extends MPTBase {

    onload(scene) {
        const myPlugin = requirePlugin('myplugin') //加载你的插件
        scene.add(
            new V(
                new H( // ① 布局构造器中可连续添加各种组件
                    new Label('测试账号：'),
                    new Input(new FormID('form-id-Account0')),
                ).align('center') //链式定义对其
                ,
                new H(
                    new Label('密码：'),
                ).aligin('center')
                .add( //还支持链式添加组件（与代码 ① 等效）
                    new Input({
                        formID: 'form-id-Password0'
                        type: 'password'
                    })
                ),
                new Button({
                    name: '测试登陆',
                    on: res => {
                        const account0 = res.form.select('form-id-Account0').value;
                        const password0 = res.form.select('form-id-Password0').value;
                        console.log(`输入的账号:${account0} 密码:${password0}`);
                    }
                })
            )
        )
    }
}
```

​		**此处的代码十分具备演示意义**，首先对于 `onload( scene )` 给出的 `scene` 实参作为默认的场景句柄，对于构建整个调试场景仅需要一条连续的链式代码即可快速实现。

​		在这串链式代码中，`new V()` 与 `new H()` 是布局器，其构造函数允许接受任意数量的 `组件` 或者 `布局器` ，同时也是支持使用布局器的 `.add( args... )` 方法连续添加新的内容。`.aligin( option )` 用于设定布局器的对其方案，H布局只有左中右（left、center、right），V布局只有上中下（top、center、bottom）策略。

​		继续关注 `new Input(new FormID('form-id-Account0'))` 处，由于是链式构造，对于 Input 组件变成了匿名 Object，后续想读取其 Value 则借助自定义的 FormID 来读取，例如这段代码：

```javascript
new Button({
	name:'测试登陆',
	on: res => {
		//其中 res 是事件投递的参数集
		//res.form.select('form-id-Account0') 即为 Input 组件的 Object
		const account0 = res.form.select('form-id-Account0').value;
		const password0 = res.form.select('form-id-Password0').value;
		console.log(`输入的账号:${account0} 密码:${password0}`);
	}
})
```



## 组件库API（Components API）

组件库中包括了布局相关的容器组件以及用于调试的交互组件。

### 布局组件

出于简便只提供 2 种布局方式：

- H布局（horizontal）

  水平布局，构造器可以使用 `new H()` 或者 `new h()`。

- V布局（vertical）

  垂直布局，构造器可以使用 `new V()` 或者 `new v()`。对于 Scene 而言，本身就是 V布局 容器，因此 Scene 将拥有 V布局 的所有API

#### construtor( Objects... ):

​		构造器支持传入多个 `组件` 或者 `布局器` ，等同于传入一个或多个 `.add( Objects... )` 方法。

#### .add( Objects... ):

​		顺次添加一个或多个 `组件` 或者 `布局器`。

案例：

```javascript
new H(label0, label1)
    .add(label2, label3, label4)
    .add(label5)
    .add(new V());
```

#### .remove( Strings/Objects... ):

​		移除容器内的特定组件，参数支持多种类型，具体如下：

- String 类型

  传入 String 类型将移除容器当前层（不含子容器）所有 FormID 为该 String 值的组件。

- FormID 类型

  与 String 类型含义相同，且 FormID 类型是允许反复创建，其指针不影响 FormID 对组件的指向。

- 组件 Object 类型

  若留存组件的实际指针，可直接传入该组件的指针精准移除。

案例：

```javascript
const h0 = new H(...);
h0.remove(
    'Form-ID-00001',
    new FormID('Form-ID-00002'),
    label0
)
```

#### .aligin( String ):

​		布局器的内部元素对其方式，H布局只有左中右（left、center、right），V布局只有上中下（top、center、bottom）策略。

案例：

```javascript
const h0 = new H(...);
h0.aligin('right');
```



### 交互组件

提供常见几种用于调试工作的交互组件：

- 按钮（Button）
- 单选框组（Radio Group）
- 开关选择器（Switch）
- 滑动选择器（Slider）
- 单行文本输入框（Input）
- 标签（Label）

以上组件除了 Radio Group 外均具备了相同的构造函数，实际上 Radio Group 也具备相同的能力，只不过初始化在 Group 上，内部每一个 Radio 的属性由额外的方法完成设置：

#### constructor( String/FormID/Object ):

​		支持传入1个 String 类型、FormID 对象或一个 `{ ... }` 结构体：

- String 类型

  对组件的 `name` 属性快速赋值

- FormID 类型

  对组件设置其 FormID 内容

  案例：

  ```javascript
  new Input(new FormID('form-id-012345'));
  ```

-  `{ ... }` 结构体

  支持一次性对多个属性同时赋值或设定不同组件的回调事件，**属性内容** 与 **事件名称** 请参考具体的组件：

  ```javascript
  new Button({
      formID: 'form-id-012345', // 或 new FormID('form-id-012345')
      name: '这是一个按钮',
      width: 600,
      height: 50,
      on: res => {
          console.log('按钮被点击了');
      }
  })
  ```



**这里只写一个组件例子，其他组件基本类同，具体设计随文档补充**

- Button（按钮）

| 属性名   | 类型     | 默认  | 说明                                                         |
| -------- | -------- | ----- | ------------------------------------------------------------ |
| formID   | FormID   | -     | 用于定位组件的自定义ID，不设置时则无ID无法被 `select()`。    |
| left     | Number   | 0     | 相对于容器左边的位置，如果父容器内存在多个组件，则该值影响的是相对于原始锚点的偏移值，该偏移值会影响容器内后面的兄弟元素定位，其他定位属性类同 |
| top      | Number   | 0     | 相对于容器右边的位置                                         |
| width    | Number   | 400   | 宽度，单位px                                                 |
| height   | Number   | 50    | 高度，单位px                                                 |
| color    | String   | black | 文字颜色                                                     |
| fontSize | Number   | 16    | 文字大小                                                     |
| bgColor  | String   | green | 背景颜色                                                     |
| on       | Callback | -     | 被点击时的回调函数                                           |
| rotate   | Number   | 0     | 基于锚点顺时针旋转弧度                                       |
| scale    | Number   | 1     | 基于锚点缩放大小                                             |



### 媒体组件

计划是支持对这些资源的直接读用，比如音频的暂停、进度条等等，设计到这里在给文档也行，总的来说就是便于小游戏环境下的这几个媒体资源的调试。

- 图片（Image）
- 音频（Audio）
- 视频（Video）
- 相机（Camera）



## 事件系统（Event）

事件分为封装事件与基础事件 2 类，其中封装事件是由基础事件根据常见的几种交互模式封装得到的，便于相关的功能调试。

### 封装事件

基础事件给出几种常用的交互方式：

- 点击事件（Click）
- 双击事件（DbClick）
- 长按事件（LongClick）
- 鼠标右键事件「PC端」
- 双指放缩事件

### 基础事件

基础事件保留了微信小游戏SDK中 emit 的相关事件原始态，便于插件开发者自行使用。

- 手指/鼠标按下（Down）
- 手指/鼠标松开（Up）
- 手指/鼠标移动（Move）

### 多Touch事件规则

通常而言组件上绑定相应的事件后，均是由单个Touch源构成的响应引发的「基础事件」，若出现多个Touch源，它们相互间的封装事件是独立产生的。若在某个组件中需要用到多Touch事件产生的组合事件，则根据基础事件自行完成事件的解析。



## 场景系统（Scene）

任何交互页面均由 Scene 来定义，初始状态下模板中给出一个默认 Scene，也允许插件开发者自行扩展。

### 默认Scene

对于一般的插件开发而言，首屏幕中插件模板给出了一个可供折叠交互的默认Scene，该Scene仅需要简单的配置即可快速满足常见的功能交互。

### 自定义Scene

当默认 Scene 不足以满足开发调试需求时，可以通过自定义 Scene 来获取 PixiJS 的绘图能力甚至底层的绘图上下文（ctx）来完成高级的场景绘制工作。

自定义Scene执行下方命令也可快速创建Scene模板：

```sh
npm run create:scene my_scene_name
```



## 图标库（Icon）

提供了常用的几种图标资源，可直接快速使用。


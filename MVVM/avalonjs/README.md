# avalonjs 国内可兼容至IE6的MVVM模式js框架

avalon 实现双向绑定的基础例子，提供理解

html:

```

<div ms-controller="main">
    <!-- 这里绑定了一个属性 -->
    <p>{{msg}}</p>
</div>

```
js:

```

var vm = avalon.define({
    $id:'main',
    msg:'我是绑定的数据，这个数据会直接展示到视图上',
});

```

### 数据流向：View->Model

html:

```

<div ms-controller="main">
    <!-- 这里绑定了input框，用户输入的信息会直接保存到vm对应的对象 -->
    <input ms-duplex="msg" />
</div>

```

js:

```

var vm = avalon.define({
    $id:'main',
    //等待用户的输入
    msg:'',
});


```

### 数据流向：双向绑定

html:

```

<div ms-controller="main">
    <!-- 这里绑定了input框，用户输入的信息会直接保存到vm对应的对象 -->
    <input ms-duplex="msg" />
    <!-- 由于input框和p都绑定了同一个vm的model，所以用户输入的内容会直接放映到p上 -->
     <p>{{msg}}</p>
</div>

```

js:

```

var vm = avalon.define({
    $id:'main',
    //等待用户的输入
    msg:'',
});


```

## 目录（ avalon version: 1.5 ）

1. 视图模型
2. 非监控属性与监控属性
3. 视图模型的作用域
4. 扫描机制
5. 指令（绑定）
6. 数据填充(ms-text, ms-html)
7. 模板绑定(ms-include)
8. 类名切换(ms-class, ms-hover, ms-active)
9. 事件绑定(ms-on，……)
10. 显示绑定(ms-visible)
11. 插入绑定(ms-if)
12. 双工绑定(ms-duplex)
13. 样式绑定(ms-css)
14. 数据绑定(ms-data)
15. 属性绑定(ms-attr)
16. 循环绑定(ms-repeat,ms-each,ms-with)
17. 动画绑定(ms-effect)
18. 自定义标签组件
19. 模块间通信及属性监控 $watch，$fire
20. 过滤器
21. 自定义指令（绑定）
22. 加载器
23. AJAX
24. 路由系统
25. 在IE6下调试avalon

### 视图模型

avalon要操作某个元素，直接在HTML上添加一些指令即可。指令里面存在某些变量，这些变量最后聚集成为一个对象，就是VM(视图模型)，我们只要操作这个VM的数据变动即可，页面会自动发生变化，这样一来我们的代码就不需要过多地区操作DOM,专至于业务本身，提高了可维护性。


avalon.define 要求对象里面必须有$id属性，它用于指定其在页面的作用范围；它会返回一个新对象，除了我们自己定义的属性和方法外，还添加了$watch,$events,$fire,$model等方法

如果要想使用单项绑定，则：
1. 该属性的名字以$开头，以$aaa，这样标识它为非监控属性。
2. 将该属性的名字放到$skipArray数组，也能标识它非监控属性，因为有的名字需要前后一致，后端不愿意加$，这种方式更灵活。
3. 有的属性我想它在这里可以多次改动，有的则显示就不改了，可以使用单向绑定，需要在ms-*属性的值前面加::，或花括号内部的前面加::

### 非监控属性与监控属性

VM对象除了包含一些必须的方法和属性外，其他的东西分为两大类，非监控属性和监控属性。

`非监控属性`：$开头变量，$skipArray中的值，某属性值为函数和元素节点，文本节点，注释节点，文档对象，文档碎片和window

`监控属性`: 值为简单的数据类型的监控属性；定义在$computed对象总的计算属性；其值类型为数组的监控数组；当其值为一个对象时，它里面的对象也继续转换为计算属性，监控属性，这个对象我们称之为子VM.

`avalon1.5版本之所以在移动端会因为手机性能跟不上而导致解绑问题，有很大一部分原因是有部分绑定是动态依赖检测的，非常消耗性能，而有部分为纯静态词法分析`

### 视图模型的作用域

在DOM里面添加一个ms-controller指令，在avalon.vmodels可以找到该VM, 这个元素下方的所有指定中的变量，都位于此VM

avalon允许我们以ms-controller进行多层嵌套，实现多个作用域的数据共享。换言之，如果某变量在当前VM找不到，他就会一直往上层找，另外还可以加入ms-important,表明就在此作用域中查找。

一些与avalon无关的script或style，可用ms-skip跳过这些区域

### 扫描机制

avalon能实现VM与视图之间的互动，最关键的东西就是这个。在有的MVVM框架，这也叫做编译(compile)，意即，将视图的某一部分的所有指令全部抽取出来，转换为一个个视图刷新函数，然后放到一个个数组中，当VM的属性变动时，就会执行这些数组的函数。当然数组里面的东西不定是函数，也可能是对象，但里面肯定有个视图刷新函数。这是MVVM框架的核心机制，但怎么抽取出来，每个框架的方式都不一样。avalon将这个过程称之为扫描。扫描总是从某个节点开始。在avalon内部，已经默认进行了一次扫描，从body元素开始描。如果我们为页面插入了什么新内容，而这个区域里面又包括了avalon指令，那么我们就需要手动扫描了。

avalon.scan是avalon第二重要的API，它有两个参数，第一个是元素节点，第二个是数组，里面为一个个VM。当然这两个参数是可选的。但当你手动扫描时，最好都会进去，这样会加快扫描速度，并减少意外。因为所有指令，都扫描后就变移除掉，这包括指定VM用的ms-controller，ms-important!

### 指令

1. `ms-attr` 
用法：如：ms-attr-value,ms-attr-disabled,ms-attr-title等等，常用的特殊用法有：ms-src,ms-href
作用：用于元素自带的属性绑定
其他：

2. `ms-active`
用法：没用过
作用：
其他：

3. `ms-controller`
用法：
作用：在页面上表现为一个特殊的属性，其属性值为ViewModel的$id，表示将在此元素或其子孙元素上圈定它的作用域范围， 但如果这些HTML存在它没有的属性，它可以向上查找上一级的ViewModel的属性。 换言之，ms-controller可以互相套嵌的。
ViewModel在DOM树的作用范围其实与CSS很相似，采取就近原则，如果当前ViewModel没有此字段 就找上一级ViewModel的同名字段，这个机制非常有利于团队协作
由于这种随机组成的方式就能实现类似继承的方式，因此我们就不必在JS代码时构建复杂的继承体系。
其他：继承的缺点也是很明显的
超类改变，子类要跟着改变，违反了“开——闭”原则
不能动态改变方法实现，不能在运行时改变由父类继承来的实现
破坏原有封装，因为基类向子类暴露了实现细节
继承会导致类的爆炸
另：为了避免未经处理的原始模板内容在页面载入时在页面中一闪而过，我们可以使用以下样式
```
.ms-controller,
.ms-important,
[ms-controller],
[ms-important] {
    visibility: hidden;
}
```

4. `ms-class`
用法：ms-class="hidden:active==true",ms-class-1="active:isCheck==true"
作用：用于绑定元素的class样式是否生效；数字还表示绑定的次序；
其他：过多的class属性绑定在移动端会因窗口高度不稳定而重新渲染导致ms-class解绑

5. ms-css
用法：ms-css-background-color="red:active==true"
作用：用于绑定元素的style属性；
其他：这个绑定属性有两个参数，但在css绑定里，相当于一个，会内部转换为js对象属性（驼峰式）

6. `ms-data`
用法：ms-data-number="number"
作用：用于将数据之间存到元素节点的data-*属性上，从data-*属性还原数据时，会简单的数据转换，再返回给你。
其他：

7. ms-duplex
用法：ms-duplex="prop",属性值只能是属性，不能是表达式
作用：ms-duplex是所有绑定属性中唯一能从V到VM同步的绑定，其余的绑定只能单向地从VM到V地同步
其他：ms-duplex还有许多分支或叫拦截器，如 ms-duplex-boolean, ms-duplex-string, ms-duplex-number, ms-duplex-checked

8. ms-each
用法：ms-each="list"
	$index:当前循环元素对应的索引值。
	$first:当前循环的元素是否为数组的第一个元素。
	$last: 当前循环的元素是否为数组的最后一个元素。
	$remove: 用于从数组中删除当前循环。
作用：avalon最早期从knockout那里引入ms-each用于循环数组， 是以当前元素的innerHTML作为模板， 生成一整片HTML
其他：一般很少使用这个指令

9. ms-effect
用法：
作用：ms-effect其实是包括两种动画操作,即进入这页面时的动画(entter),及离开这页面时的动画(leave)；在avalon中,我们可以轻松使用ms-if, ms-visble, ms-include, ms-repeat, ms-each, ms-with触发动画.具体来说, ms-if在元素插入DOM树时调用enter动画,移出DOM树时调用leave动画;ms-visible在其真值时调用enter动画,其假值时调用leave动画; ms-include有点特别,enter动画与leave动画同时进行,被移出的视图执行leave动画,正在插入的视图执行enter动画; ms-repeat, ms-with, ms-each是对一组元素进行添加删除移动,其中添加与移动操作是执行enter动画,删除是执行leave动画, 因此是一组动画同时,会有点乱,于是提供了data-effect-stagger辅助指令错开每个动画,让整体看起来更加賞心悦目!(duration要自带)
其他：视实现不同,一共分为三种:CSS3 transition动画,CSS3 animation动画, JS动画
实例：

```JavaScript
avalon.effect("xxx", {
    enterClass: "ng-enter",
    leaveClass: "ng-leave"
})

avalon.effect("flip", {
    enterClass: "flip-en xxx", //可以使用多个类名
    leaveClass: "flip-le yyy"
})

avalon.effect("bounce", {
    enterClass: "bounce-in",
    leaveClass: "bounce-out"
})

//随机执行各种动画效果

var ani = ["rotate-in", "flip", "zoom-in", "roll", "speed", "elastic"]
var lastIndex = NaN

function getRandomAni() {
    while (true) {
        var index = ~~Math.random() * 6
        if (index != lastIndex) {
            lastIndex = index
            return ani[index]
        }
    }
}
avalon.effect("animate", {
    enterClass: getRandomAni,
    leaveClass: getRandomAni
})

//js是留给IE6-8使用的,参数(el:进行动画的元素,done:动画完成的回调), 务必都用上它们.

avalon.effect("toggle", {
  enter: function(elem, done) {
      $(elem).show(500, done)
  },
  leave: function(elem, done) {
      $(elem).hide(500, done)
  }
})

```
```Html
<div ms-effect="toggle" ms-visible="bool" class="box"></div>
```

10. ms-html
用法：
作用：
其他：

11. ms-hover
用法：ms-hover="active:isCheck==true"
作用：用于鼠标浮于上方的样式展示
其他：不太好用，直接用ms-class代替就行

12. ms-if
用法：ms-if="isCheck==true"
作用：ms-if与ms-each,ms-with,ms-repeat归类为流程绑定，如果表达式为真值那么就将当前元素输出页面，不是就将它移离原位置。 它的效果与上一章节的ms-visible效果看起来相似的， 但它会影响到:empty伪类，并能更节约性能。
其他：在一个元素中, ms-if与ms-repeat是可以同时存在于某一元素, 但ms-if优先级比ms-repeat高; 如果ms-if的表达式为false,那么就会导致所有东西循环不出来.当我们只想循环出数组或对象的某一部分符合元素的项时, 我们需要一个优先及比ms-if,ms-repeat都低的特殊绑定,这时ms-if-loop就被发明出来了.它要来它必须与ms-repeat位于同一元素上. 用法与ms-if相同.

13. ms-important
用法：ms-important="VMid"
作用：限制当前VM不会往父节点向上查找属性
其他：在选择是继承还是组合的问题上，avalon倾向组合。组合的使用范例就是CSS，因此也有了ms-important
另：为了避免未经处理的原始模板内容在页面载入时在页面中一闪而过，我们可以使用以下样式
```
.ms-controller,
.ms-important,
[ms-controller],
[ms-important] {
    visibility: hidden;
}
```

14. ms-include
用法：对于页面内的模板，我们可以使用ms-include=”expr”绑定，对于独立于页面的模板，我们可以使用ms-include-src=”expr”绑定。
作用：
其他：ms-include与ms-include-src的属性值可以添加插值表达式，见下面例子，不过注意需要打开服务器，因为用到AJAX请求。
在模板加载后，加工一下模板，可以使用data-include-loaded来指定回调的名字。
在模板扫描后，隐藏loading什么的，可以使用data-include-rendered来指定回调的名字。
avalon.templateCache，所有ms-include-src加载的模板都会缓存在这里，从而有效地减少请求数。 并且这个东西还有一个额外的好处，我们的JS与CSS最终会压缩合并，对于这些模板我们也想把它们合并到JS文件里面， 它就有用武之地了。这也是我们在第一节看到的那样，把requirejs加载回来的模板都放在avalon.templateCache里， 与ms-include-src一起使用了。
一个辅助指令data-include-replace，当其值为真，它就会加载模板后自动替代掉其原父容器节点。
data-include-cache="true"辅助指令，它会保存之前的模板被扫描后的DOM节点。

实例：评论树
```
<!DOCTYPE html>
<html>

<head>
    <title>TODO supply a title</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <script src="avalon.js"></script>
    <style>
        .level_even {
            background: #ccc;
        }
        
        .level_odd {
            background: #fff;
        }
    </style>
    <script>
        var array = [{
            children: [],
            author: "第一层a"
        }, {
            children: [],
            author: "第一层b"
        }];
        array[0].children.push({
            children: [],
            author: "第二层a"
        }, {
            children: [],
            author: "第二层b"
        })
        array[0].children[0].children.push({
            children: [],
            author: "第三层a"
        }, {
            children: [],
            author: "第三层b"
        })
        array[0].children[0].children[0].children.push({
            children: [],
            author: "第四层a"
        }, {
            children: [],
            author: "第四层b"
        }, {
            children: [],
            author: "第四层c"
        })
        var vm = avalon.define({
            $id: "test",
            aaa: "通过ms-repeat, ms-include实现树",
            array: array,
            addClass: function() {
                var level = 0
                var parent = this.parentNode
                do {
                    if (parent.tagName == "UL") {
                        level++
                    }
                    if (parent.tagName === "BODY") {
                        break
                    }
                } while (parent = parent.parentNode)
                avalon(this).addClass("level_" + level)
                avalon(this).addClass(level % 2 === 0 ? "level_even" : "level_odd")
            }
        })
    </script>
</head>

<body ms-controller="test">
    <h2>{{aaa}}</h2>
    <script type="avalon" id="comments">
        <ul>
            <li ms-repeat="el.children">
                {{el.author}}
                <div ms-if="el.children.size()" ms-include="'comments'" data-include-rendered='addClass'> </div>
            </li>
        </ul>
    </script>
    <div class='level_0 level_even'>
        <ul>
            <li ms-repeat="array">
                {{el.author}}
                <div ms-if="el.children.size()" ms-include="'comments'" data-include-rendered='addClass'></div>
            </li>
        </ul>
    </div>
</body>

</html>

```

15. ms-on
用法：
 ms-click不是简单的onclick的别名，它在内部屏蔽了浏览器的差异，并且对许多浏览器暂时不支持的事件做了兼容处理。
 ms-on-click="fn"可以写成ms-click="fn",默认fn的第一个参数就是事件对象,如果想传更多参数, 可以改成这样ms-click="fn(111,aaa,$event)".
 其他的还有
 > animationend、 blur、 change、 input、 click、 dblclick、 focus、 keydown、 keypress、 keyup、 mousedown、 mouseenter、 mouseleave、 mousemove、 mouseout、 mouseover、 mouseup、 scan、 scroll、 submit
 事件绑定的属性值的格式，必须是函数名或函数名后＋小括号(ms-on-click="aaa.fn"是不被允许的)。 小括号不是必须的。它用于添加参数，并且有一个特殊的$event参数，指向事件对象。如果不写小括号，默认第一个参数就是事件对象。
作用：
其他：注意:ms-duplex与ms-input不能用在同一元素上。
avalon的事件绑定支持多投事件机制（同一个元素可以绑定N个同种事件，如ms-click=fn, ms-click-1=fn2, ms-click-2=fn3）
avalon已经对ms-mouseenter, ms-mouseleave进行修复，可以在这里与这里了解这两个事件。到chrome30时，所有浏览器都原生支持这两个事件。
最后是mousewheel事件的修改，主要问题是出现firefox上，它死活也不愿意支持mousewheel，在avalon里是用DOMMouseScroll或wheel实现模拟的。我们在事件对象通过wheelDelta属性是否为正数判定它在向上滚动。
解除ms-on-*, ms-click等绑定的事件回调，就直接将VM中对应的函数置换为空函数就行了！

16. ms-repeat
用法：ms-repeat="list";
	$index:当前循环元素对应的索引值。
	$first:当前循环的元素是否为数组的第一个元素。
	$last: 当前循环的元素是否为数组的最后一个元素。
	$remove: 用于从数组中删除当前循环。
	$key: 当前循环中的某一个键名，此为一个不可监控的属性。
	$val: 当前循环中的某一个键值。
作用：avalon从angular那里引入ms-repeat，它们自行根据对象的内部，内部执行ms-each或ms-with的逻辑， 不过ms-repeat是以当前元素的outerHTML作为模板，生成一整片HTML
其他：有辅助函数来处理渲染时机

17. ms-skip
用法：
作用：注明这块区域不应用任何的ViewModel的属性，它里面的任何指令（绑定属性）都会失效。 因为{{}}也算一种指令，而任何指令在被扫描后都会被移除，如果我们想保留某个区域的{{}}，就需要用到ms-skip。
其他：很少用

18. ms-text
用法：ms-text="prop"
作用：用于设置元素内的文本内容，主要是防止页面没有渲染完成就看到插值符
其他：ms-text与ms-html其实就是{{prop}}、{{prop|html}}的真身
{{prop | html}}其实是加一个过滤器，也只有文本节点中的插值表达式可以加各种过滤器实现各种功能。
不过ms-text、 ms-html作为一个绑定属性，必须附于元素节点之上，因此没有前者那么方便。

19. ms-visible
用法：
作用：avalon通过ms-visible="expr"实现对某个元素显示隐藏控制，它的效果类拟于jQuery的toggle， 如果它后面跟着的表达式为真值时则显示它所在的元素，为假值时则隐藏。 不过想显示一个元素不是直接 display:block这么简单，众所周知， display拥有inline, inline-block, block, list-item, table, table-cell等十来个值， 比如用户之前是让此LI元素表示inline-block，实现水平菜单效果，你直接display:block就会撑破布局。 因此元素之前是用什么样式显示，需要保存下来，当表达式转换为真值时再还原。
其他：

20. ms-with
用法：ms-with="object";
	$key: 当前循环中的某一个键名，此为一个不可监控的属性。
	$val: 当前循环中的某一个键值。
作用：ms-with用于循环对象,是以当前元素的innerHTML作为模板， 
其他：一般很少使用这个指令

22. 自定义标签
用法：
作用：avalon1.5新添加的API,avalon.directive, 用于快速创建一种全新的指令!
其他：
```
avalon.directive(name, {
  init: function(binding){
     //在这里处理binding.expr属性
  },
  update: function(value, oldValue){
  //value是binding.expr经过解析得到的当前VM属性的值
  //oldValue是它之前的值
  },
})

//拿最简单的data绑定来说
avalon.directive("data", {
    priority: 100,
    update: function (val) {
        var elem = this.element
        var key = "data-" + this.param
        if (val && typeof val === "object") {
            elem[key] = val
        } else {
            elem.setAttribute(key, String(val))
        }
    }
})
```

23. 辅助指令
data-xxx-yyy="xxx"，，比如ms-duplex的某一个辅助指令为data-duplex-event="change"，ms-repeat的某一个辅助指令为data-repeat-rendered="yyy"

### 自定义标签组件

以标签的形式声明组件比起在JS里以类的形式创建组件，来得更简单明了。

做这套东西有几个难点：

* 生命周期管理，从构建配置对象，到模板微调，到初始化，到子组件就绪，到自身组件就绪，到组件销毁，都有自己相应的回调。
* 继承机制
* 当前组件必须等到子组件就绪才闭合自身，比如父组件的高度是由子组件决定
* 组件的某些很大块的区块如何定义（这个引入插入点的概念处理），如弹出层有title, content, footer

为了让一个页面使用多个UI库，avalon引入了命名空间的概念，这是来自早期XML的设计。一个标签名由冒号隔开，前面是命名空间，后面是标签名。

在定义一个UI组件，最少有3个文件，JS文件（引用其他两个文件或更多子组件的JS），HTML文件（组件的模块），CSS文件（它可以由SASS更高级的语言生成）。

avalon.component是用来定义一个组件，第一个参数为自定义标签的名字（包括命名空间），第二个是配置对象，里面定义这个组件用到的属性，状态与方法。

实例：构建组件
```
avalon.component("ms:button", {
    a: 1,
    $replace: 1,
    $ready: function() {
        console.log("BUTTON构建完成")
    },
    $template: "{{a}}"
})

```
配置项有：
* $replace: Boolean, 真值时表示替换其容器
* $slot: String 默认插入点的名字
* $template: String 组件的模板
* $extend: String 指定要继承的组件名
* $container: DOM 插入元素的位置,比如dialog就不一定在使用它的位置插入,通常放在body中
* $construct: Function 用于调整三个配置项的合并,默认是function:(a, b,c ){return avalon.mix(a, b,c)}
* $$template Function 用于微调组件的模板,传入$template与当前元素
* $init: Function 刚开始渲染时调用的回调, 传入vm与当前元素
* $childReady: Function 当其子组件$ready完毕后, 会冒泡到当前组件触发的回调, 传入vm与当前元素
* $ready: Function 当组件的所有子组件都调用其$ready回调后才触发的回调, 传入vm与当前元素
* $dispose: Function 该组件被移出DOM树，并且元素不存在msRetain属性时的销毁回调, 传入vm与当前元素

当vm构建好时, $replace, $slot, $template,$container, $construct会消失

所有回调的执行顺序
`组件自身的$$template --> 组件自身的$init --> 库的全局$init -->组件自身的$childReady --> 库的全局$childReady -->组件自身的$ready --> 库的全局$ready -->
组件自身的$dispose --> 库的全局$dispose`

我们可以在

* $init中添加各种$watch回调, 为IE6-8的VBscript函数的this指向不正确进行bind fix
* $ready中重新计算组件的高宽
* $dispose中将VM中的元素节点置为null，移除各种dom事件，清空元素内部，方便GC

此外，每个组件VM，还添加了一个叫 $refs 的非监控对象属性，用于存放子组件的VM。



### 过滤器

avalon从angular中抄来管道符风格的过滤器，但有点不一样。 它只能用于{{}}插值表达式。如果不存在参数，要求直接跟|filter，如果存在参传，则要用小括号括起，参数要有逗号，这与一般的函数调用差不多，如|truncate(20,"……")

avalon自带以下几个过滤器

1. html
没有传参，用于将文本绑定转换为HTML绑定
2. sanitize
去掉onclick, javascript:alert等可能引起注入攻击的代码。
3. uppercase
大写化
4. lowercase
小写化
5. truncate
对长字符串进行截短，truncate(number, truncation), number默认为30，truncation为“...”
6. camelize
驼峰化处理
7. escape
对类似于HTML格式的字符串进行转义，把尖括号转换为&gt; &lt;
8. currency
对数字添加货币符号，以及千位符， currency(symbol)
number
对数字进行各种格式化，这与与PHP的number_format完全兼容， number(decimals, dec_point, thousands_sep),
decimals	可选，规定多少个小数位。
dec_point	可选，规定用作小数点的字符串（默认为 . ）。
thousands_sep	可选，规定用作千位分隔符的字符串（默认为 , ），如果设置了该参数，那么所有其他参数都是必需的。
            
date
对日期进行格式化，date(formats)
```
生成于{{ new Date | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ "2011/07/08" | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ "2011-07-08" | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ "01-01-2000" | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ "03 04,2000" | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ "3 4,2000" | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ 1373021259229 | date("yyyy MM dd:HH:mm:ss")}}

生成于{{ "1373021259229" | date("yyyy MM dd:HH:mm:ss")}}

```
多个过滤器一起工作
```
<div>{{ prop | filter1 | filter2 | filter3(args, args2) | filter4(args)}}</div>
```

自定义过滤器
```
avalon.filters.myfilter = function(str, args, args2){//str为管道符之前计算得到的结果，默认框架会帮你传入，此方法必须返回一个值
        /* 具体逻辑 */
        return ret;
     }
//默认过滤器
   avalon.filters.default = function(str, defaultStr){
      return str || defaultStr
   }
 //性别过滤器
   avalon.filters.sex = function(str){
       return str == '0' ? '男': '女'
    }
```


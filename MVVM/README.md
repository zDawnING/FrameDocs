# MVVM 是Model - View - ViewModel的简写

# 序言

## MVC，MVP 和 MVVM

### MVC
Model（模型）、View（视图）和Controller（控制）
以上三层不论简单或者复杂，从结构上看都可解析为3层

1. 顶层：View 直接面向用户的视图层，提供给用户操作界面
2. 底层：Model 核心数据层，程序需要操作的数据或信息
3. 中间层：Controller 负责根据用户操作的视图指令，选取数据层中的数据，并做一系列的逻辑操作

这三层相互间紧密联系，但又相互独立，每一层的内部变化都不影响其他层，每一层都对外提供一个接口供上一层调用，
这样可以做到修改外观或者修改数据都不会影响到其他层，大大方便了维护和升级。

各部分间的通讯都是单向的：`View --> Controller --> Model --> View`
1. View 传送指令到 Controller
2. Controller 完成业务逻辑后，要求 Model 改变状态
3. Model 将新的数据发送到 View，用户得到反馈

还有衍生的互动模式：
1. 接受用户请求，通过Controller接受指令。`user >  Controller --> Model --> View`

例子：以backbone.js为例
	1. 用户可以向View发送指令（Dom事件），再由View直接要求Model改变状态（user > View <--> Model ）
	2. 用户可以直接向Controller发送指令（改变URL触发hashChange事件），再由Controller发送给View
	3. Controller非常轻，只起到路由作用，而View非常厚，业务逻辑都部署在View中。所以Backbone索性取消了Controller，只保留了一个Router路由器

### MVP
Model（模型）、View（视图）和Presenter（由Controller改名而来）

各部分间的通讯都是双向的： View <--> Presenter <--> Model
View非常轻，不部署任何业务逻辑，称为“被动视图”（Passive View），即没有任何主动性，而Presenter非常重，所有逻辑都部署子那里

### MVVM

Model（模型）、View（视图）和ViewModel（由Presenter改名而来）

各部分间的通讯都是双向的：View <--> ViewModel <--> Model
与MVP的区别是，它采用双向绑定（data-binding）:View的变动，自动反映在ViewModel,View与Model通过ViewModel实现双向绑定，所以View和Model的变动会相互影响到对方
(Angular,Vue,Ember,Avalon等等MVVM框架)



# FIS3 前端的工程构建工具。

安装 FIS3 或重装
npm install -g fis3

升级
npm update -g fis3

### 构建
FIS3 的构建不会修改源码，而是会通过用户设置，将构建结果输出到指定的目录。
进入项目根目录，执行命令，进行构建
项目根目录：FIS3 配置文件（默认fis-conf.js）所在的目录为项目根目录。
```
fis3 release -d <path>
```

#### 资源定位

资源定位能力，可以有效地分离开发路径与部署路径之间的关系，工程师不再关心资源部署到线上之后去了哪里，变成了什么名字，这些都可以通过配置来指定。而工程师只需要使用相对路径来定位自己的开发资源即可。这样的好处是 资源可以发布到任何静态资源服务器的任何路径上，而不用担心线上运行时找不到它们，而且代码 具有很强的可移植性，甚至可以从一个产品线移植到另一个产品线而不用担心线上部署不一致的问题。

在默认不配置的情况下只是对资源相对路径修改成了绝对路径。通过配置文件可以轻松分离开发路径（源码路径）与部署路径。

#### 配置文件

默认配置文件为 fis-conf.js，FIS3 编译的整个流程都是通过配置来控制的。FIS3 定义了一种类似 CSS 的配置方式。固化了构建流程，让工程构建变得简单。

fis.match()  设置规则的配置接口

```
fis.match(selector, props);
```
* `selector` ：FIS3 把匹配文件路径的路径作为selector，匹配到的文件会分配给它设置的 props。关于 selector 语法，请参看 Glob 说明
* `props` ：编译规则属性，包括文件属性和插件属性

#### 规则覆盖

假设有两条规则 A 和 B，它俩同时命中了文件 test.js，如果 A 在 B 前面，B 的属性会覆盖 A 的同名属性。


fis.media()

fis.media() 接口提供多种状态功能，比如有些配置是仅供开发环境下使用，有些则是仅供生产环境使用的。

例子：
```
fis.media('prod').match('*.js', {
  optimizer: fis.plugin('uglify-js')
})

`fis3 release prod `
//使用prod指定的编译配置，即对js进行压缩
//因此可以配置多个media配置，多个状态，一个状态一个配置
//另外 media dev 已被占用，不加media参数时默认为dev
```

执行 `fis3 inspect `来查看文件命中属性的情况.
查看文件分配到的属性，这些属性决定了文件将如何被编译处理

`fis3 inspect <media>` 查看特定 media 的分配情况


#### 文件指纹

文件指纹，唯一标识一个文件。在开启强缓存的情况下，如果文件的 URL 不发生变化，无法刷新浏览器缓存。一般都需要通过一些手段来强刷缓存，一种方式是添加时间戳，每次上线更新文件，给这个资源文件的 URL 添加上时间戳。

而 FIS3 选择的是添加 MD5 戳，直接修改文件的 URL，而不是在其后添加 query。
```
//清除其他配置，只剩下如下配置
fis.match('*.{js,css,png}', {
  useHash: true
});
```

#### 压缩资源

通过压缩器对 js、css、图片进行压缩是一直以来前端工程优化的选择。

```
// 清除其他配置，只保留如下配置
fis.match('*.js', {
  // fis-optimizer-uglify-js 插件进行压缩，已内置
  optimizer: fis.plugin('uglify-js')
});

fis.match('*.css', {
  // fis-optimizer-clean-css 插件进行压缩，已内置
  optimizer: fis.plugin('clean-css')
});

fis.match('*.png', {
  // fis-optimizer-png-compressor 插件进行压缩，已内置
  optimizer: fis.plugin('png-compressor')
});
```

#### CssSprite图片合并

FIS3 构建会对 CSS 中，路径带 ?__sprite 的图片进行合并。为了节省编译的时间，分配到 useSprite: true 的 CSS 文件才会被处理。

* 默认情况下，对打包 css 文件启动图片合并功能。

实例:
```
li.list-1::before {
  background-image: url('./img/list-1.png?__sprite');
}

li.list-2::before {
  background-image: url('./img/list-2.png?__sprite');
}

// 启用 fis-spriter-csssprites 插件
fis.match('::package', {
  spriter: fis.plugin('csssprites')
})

// 对 CSS 进行图片合并
fis.match('*.css', {
  // 给匹配到的文件分配属性 `useSprite`
  useSprite: true
})

```

#### 功能组合

 FIS3 做压缩、文件指纹、图片合并、资源定位，现在把这些功能组合起来
```
// 加 md5
fis.match('*.{js,css,png}', {
  useHash: true
});

// 启用 fis-spriter-csssprites 插件
fis.match('::package', {
  spriter: fis.plugin('csssprites')
});

// 对 CSS 进行图片合并
fis.match('*.css', {
  // 给匹配到的文件分配属性 `useSprite`
  useSprite: true
});

fis.match('*.js', {
  // fis-optimizer-uglify-js 插件进行压缩，已内置
  optimizer: fis.plugin('uglify-js')
});

fis.match('*.css', {
  // fis-optimizer-clean-css 插件进行压缩，已内置
  optimizer: fis.plugin('clean-css')
});

fis.match('*.png', {
  // fis-optimizer-png-compressor 插件进行压缩，已内置
  optimizer: fis.plugin('png-compressor')
});
```
有时候开发的时候不需要压缩、合并图片、也不需要 hash。那么给上面配置追加如下配置；

```
fis.media('debug').match('*.{js,css,png}', {
  useHash: false,
  useSprite: false,
  optimizer: null
})
```

### 调试

`fis3 server open` 开启内置web server

`fis3 release` 不加-d 则默认发布至内置web server 的根目录下


`fis3 server start` 启动本地web server

FIS3 内置的 Server 是常驻的，如果不重启计算机或者调用命令关闭是不会关闭的。

`fis3 release -w` 开启监听文件变化发布

`fis3 release -wL` 开启浏览器自动刷新

#### 发布至远程机，具体参考官网API

配置项为：
```
fis.match('*', {
  deploy: fis.plugin('http-push', {
    receiver: 'http://cq.01.p.p.baidu.com:8888/receiver.php',
    to: '/home/work/htdocs' // 注意这个是指的是测试机器的路径，而非本地机器
  })
})
```

#### 快捷设置发布命令

例：
```
fis3 release -d /Users/my-name/work/htdocs

//可转换为： fis release
fis.match('*', {
  deploy: fis.plugin('local-deliver', {
    to: '/Users/my-name/work/htdocs'
  })
})
```

### 内容嵌入

html中嵌入图片base64
```
<img title="百度logo" src="images/logo.gif?__inline"/>
```

html中嵌入样式文件
```
<link rel="stylesheet" type="text/css" href="demo.css?__inline">
```

html中嵌入脚步资源
```
<script type="text/javascript" src="demo.js?__inline"></script>
```

html中嵌入页面文件
```
<link rel="import" href="demo.html?__inline">
```

js中嵌入js文件
```
__inline('demo.js');
```

js中嵌入图片base64
```
var img = __inline('images/logo.gif');
```

js中嵌入其他文本文件
```
var css = __inline('a.css');
```

css中嵌入其他css
```
@import url('demo.css?__inline');
```

css中嵌入图片base64
```
.style {
    background: url(images/logo.gif?__inline);
}
```

### 定位资源

FIS3 支持对html中的script、link、style、video、audio、embed等标签的src或href属性进行分析，一旦这些标签的资源定位属性可以命中已存在文件，则把命中文件的url路径替换到属性中，同时可保留原来url中的query查询信息。

甚至可以让 url和发布目录不一致

```
fis.match('*.{js,css,png,gif}', {
    useHash: true // 开启 md5 戳
});

// 所有的 js
fis.match('**.js', {
    //发布到/static/js/xxx目录下
    release : '/static/js$0',
    //访问url是/mm/static/js/xxx
    url : '/mm/static/js$0'
});

// 所有的 css
fis.match('**.css', {
    //发布到/static/css/xxx目录下
    release : '/static/css$0',
    //访问url是/pp/static/css/xxx
    url : '/pp/static/css$0'
});

// 所有image目录下的.png，.gif文件
fis.match('/images/(*.{png,gif})', {
    //发布到/static/pic/xxx目录下
    release: '/static/pic/$1',
    //访问url是/oo/static/baidu/xxx
    url : '/oo/static/baidu$0'
});
```

### 声明依赖（特色）

声明依赖能力为工程师提供了声明依赖关系的编译接口。

FIS3 在执行编译的过程中，会扫描这些编译标记，从而建立一张 静态资源关系表，资源关系表详细记录了项目内的静态资源id、发布后的线上路径、资源类型以及 依赖关系 和 资源打包 等信息。使用 FIS3 作为编译工具的项目，可以将这张表提交给后端或者前端框架去运行时，根据组件使用情况来 按需加载资源或者资源所在的包，从而提升前端页面运行性能。

实例：

在项目的index.html里使用注释声明依赖关系
```
<!--
    @require demo.js
    @require "demo.css"
-->
```
默认情况下，只有js和css文件会输出到manifest.json表中，如果想将html文件加入表中，需要通过配置 useMap 让HTML文件加入 manifest.json
```
//fis-conf.js
fis.match('*.html', {
    useMap: true
})
```
配置以下内容到配置文件进行编译
```
// fis-conf.js
fis.match('*.html', {
    useMap: true
});

fis.match('*.{js,css}', {
    // 开启 hash
    useHash: true
});
```
查看 output 目录下的 manifest.json 文件
```
{
    "res" : {
        "demo.css" : {
            "uri" : "/static/css/demo_7defa41.css",
            "type" : "css"
        },
        "demo.js" : {
            "uri" : "/static/js/demo_33c5143.js",
            "type" : "js",
            "deps" : [ "demo.css" ]
        },
        "index.html" : {
            "uri" : "/index.html",
            "type" : "html",
            "deps" : [ "demo.js", "demo.css" ]
        }
    },
    "pkg" : {}
}
```
js和css同理，但是对于js:
* 注意， require() 不再处理，js 中 require() 留给各种前端模块化方案，假设你选择的是 AMD 那么就得解析，require([]) 和 require()；如果选用的是 mod.js 那么就得解析 require.async() 和 require()，其他亦然。







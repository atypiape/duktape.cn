# Duktape 介绍


*译文作者：[Atypiape][atypiape]* &nbsp;&nbsp; *原文来自：[Duktape][duktape]* &nbsp;&nbsp; *翻译日期：2016-08-12*

[atypiape]: http://www.atypiape.com
[duktape]: http://www.duktape.org


Duktape 是一个轻量级的嵌入式 JavaScript 引擎，专注于可移植性和低占用率。

Duktape 可以被轻松地集成进一个 C/C\++ 项目中：只需要将 duktape.c、duktape.h 和 duk\_config.h 三个文件加入你的构建项目中，并使用 Duktape API 来实现 C 代码与 ECMAScript 函数的双向调用。


### 主要特性

- 可嵌入、可移植、低占用：能够运行在 256kB 闪存（Flash）和 64kB 系统内存（RAM）的平台上；
- 兼容 ECMAScript E5/E5.1 标准，并借鉴了 ECMAScript E6 的部分特性
- 支持 Khronos/ES6 的 TypedArray 对象和 Node.js 的 Buffer 对象
- 内置调试器
- 内置正则表达式引擎
- 内置 Unicode 支持
- 最小限度的平台依赖
- 整合了引用计数机制和 mark-and-sweep 垃圾回收机制
- 协同程序(coroutines)
- 属性（property）虚拟化中使用了 ECMAScript E6 Proxy 对象的一个子集
- 以字节码转储（dump）/加载的方式，来对已编译函数进行缓存
- 可发布版本中包含一个可选的日志框架，基于 CommonJS 的模块加载来框架，等等
- 采用 MIT 自由许可协议


### 代码和内存占用

 _        | x86 默认 | x86 低端内存 | x86 全低端内存
--------- | -------- | ------------| ---------------
 代码     | 170kB     | 150kB      | 200kB
 启动内存 | 68kB      | 35kB        | 4kB

请参考 [GCC 选项][GCC_OPTIONS]中的最小化代码占用。全[低端内存][LOWMEM]选项使用了“指针压缩”和基于 ROM 的字符串/对象。基于 ROM 的字符串/对象也可以用于其他非低端内存选项。


 [GCC_OPTIONS]: https://github.com/svaarala/duktape/blob/master/doc/low-memory.rst#optimizing-code-footprint "GCC 选项"
 [LOWMEM]: https://github.com/svaarala/duktape/blob/master/doc/low-memory.rst "低端内存"


### 当前状态

- 稳定


### 技术支持

- Duktape Wiki: [wiki.duktape.org][WIKI]
- 用户问答社区: Stack Overflow 上的 [duktape][STACK_OVERFLOW] 标签
- Bugs 和功能需求: [GitHub issues][GITHUB_ISSUES]
- 综合讨论区: chat.freenode.net ([webchat][WEBCHAT]) 上的 IRC 频道 #duktape

[WIKI]: http://wiki.duktape.org/ "wiki.duktape.org"
[STACK_OVERFLOW]: http://stackoverflow.com/questions/tagged/duktape "Stack Overflow"
[GITHUB_ISSUES]: https://github.com/svaarala/duktape/issues "GitHub issues"
[WEBCHAT]: https://webchat.freenode.net/ "webchat"


### 使用 Duktape 的项目

参见：[使用 Duktape 的项目][PROJECTS]

如果你在自己的项目中使用了 Duktape，可以发送一封邮件或者开一个 GitHub issue 来将项目加到列表中。

[PROJECTS]: http://wiki.duktape.org/ProjectsUsingDuktape.html "使用 Duktape 的项目"


### 类似的引擎

有多个与 Duktape 定位类似的 JavaScript 引擎，至少有如下几个: 

- [Espruino][Espruino] (MPL v2.0)
- [JerryScript][JerryScript] (Apache License v2.0)
- [MuJS][MuJS] (Affero GPL)
- [quad-wheel][quad-wheel] (MIT License)
- [tiny-js][tiny-js] (MIT license)
- [v7][v7] (GPL v2.0)

也可以参考 [ECMAScript 引擎列表][engines-list]。


[Espruino]: https://github.com/espruino/Espruino "Espruino"
[JerryScript]: http://samsung.github.io/jerryscript/ "JerryScript"
[MuJS]: http://mujs.com/ "MuJS"
[quad-wheel]: https://code.google.com/p/quad-wheel/ "quad-wheel"
[tiny-js]: https://github.com/gfwilliams/tiny-js "tiny-js"
[v7]: https://github.com/cesanta/v7 "v7"
[engines-list]: https://en.wikipedia.org/wiki/List_of_ECMAScript_engines ""

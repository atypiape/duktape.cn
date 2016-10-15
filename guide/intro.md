## 概述


### 文档范围

本指南概述了如何在你的程序中使用 Duktape。一旦你熟悉了这些基础知识，便可通过简明的 [API 参考文档][API reference] 来查询 API 详情。而 [Duktape Wiki][Duktape Wiki] 则提供了更详细的例子和最佳实现。

[API reference]: api.html "API reference"
[Duktape Wiki]: http://wiki.duktape.org "Duktape Wiki"

本文档并未包含 Duktape 的内部实现细节，如果你希望修改它们，可以查看 [Duktape 代码库][Duktape repo]。

[Duktape repo]: https://github.com/svaarala/duktape/tree/master/doc "Duktape repo"


### Duktape 是什么？

Duktape 是一个嵌入式的 Ecmascript E5/E5.1 引擎，专注于可移植性和低占用率。通过将 Duktape 集成到你的 C/C++ 程序中，你可以轻松的借助编写脚本来扩展其功能。也可以在 Ecmascript 中构建程序的主控制流，并将繁重的任务交由快速的 C 代码来处理。

可嵌入性意味着 Duktape 对底层平台及其功能做了最小的假设；取而代之，由嵌入 Duktape 的应用程序来控制提供哪些原生绑定，以何种方式进行绑定。 例如，将文本打印到控制台、与文件系统进行交互，这些功能默认并未绑定。Duktape 的可发布包中含有提供绑定的例子，如果它们适用于你的需求，你可以轻松的进行集成。

术语 Ecmascript 和 Javascript 通常被认为或多或少是等价的。然而，从技术上来讲，Javascript 及其变种只是 Ecmascript 语言的一个运用环境。在实践中，两者之间的界限并不是很明确: 即使那些非浏览器的 Ecmascript 环境，通常也提供一些浏览器特有的内置扩展。即便如此，我们这里使用的术语 Ecmascript 特指由 Duktape 实现的语言。

### 一致性

Duktape 符合下列 Ecmascript 规范:

- [Edition 5 (E5)][E5]
- [Edition 5.1 (E5.1)][E5.1] (as [HTML][E5.1-HTML])

[E5]: http://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262%205th%20edition%20December%202009.pdf "Edition 5"
[E5.1]: http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf "Edition 5.1"
[E5.1-HTML]: http://www.ecma-international.org/ecma-262/5.1/ "Edition 5.1 as HTML"

Duktape 借鉴了 Ecmascript E6 的[几个特性][a-few-features]:

- [Edition 6 (E6)][E6] (as [HTML][E6-HTML])

[a-few-features]: #es6features "a few features"
[E6]: http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf "Edition 6"
[E6-HTML]: http://www.ecma-international.org/ecma-262/6.0/index.html "Edition 6 as HTML"

对 TypedArray 的支持，是基于 Khronos TypedArray 规范，并在 ES6 TypedArray 的基础上解决了规范中一些模棱两可的语法 (和其他引擎做比较): 

- [Typed Array 规范 (2013-8-18 编辑草案)][typedarray-specs] (注: 最新链接)
- `svn co -r 30720 https://cvs.khronos.org/svn/repos/registry/trunk/public/typedarray`

[typedarray-specs]: https://www.khronos.org/registry/typedarray/specs/latest/ "Typed Array Specification"

对 Node.js 的 Buffer 支持是基于:

- [Buffer Node.js v0.12.1][buffer-nodejs]

[buffer-nodejs]: https://nodejs.org/docs/v0.12.1/api/buffer.html "Buffer Node.js v0.12.1"



<h2>Features</h2>

<p>Besides standard Ecmascript features, Duktape has the following additional
features (some are visible to applications, while others are internal):</p>
<ul>
<li>Khronos/ES6 <a href="https://www.khronos.org/registry/typedarray/specs/latest/">TypedArray</a>
    and <a href="https://nodejs.org/docs/v0.12.1/api/buffer.html">Node.js Buffer</a> bindings,
    plain buffer type (lightweight ArrayBuffer)</li>
<li>Borrowed from ES6: <code>setPrototypeOf</code>/<code>__proto__</code>,
    a subset of <code>Proxy</code> objects, and minimal <code>const</code> support</li>
<li>Duktape specific built-ins: provided by the <code>Duktape</code> global object</li>
<li>Extended types: custom "buffer" and "pointer" types, extended string type
    which supports arbitary binary strings and
    non-<a href="http://en.wikipedia.org/wiki/Plane_(Unicode)#Basic_Multilingual_Plane">BMP</a>
    strings (standard Ecmascript only supports 16-bit codepoints)</li>
<li>Combined reference counting and mark-and-sweep garbage collection,
    with finalizers and emergency garbage collection (you can also build
    with just mark-and-sweep)</li>
<li>Coroutine support</li>
<li>Tail call support</li>
<li>Built-in debugger framework based on a debugger protocol</li>
<li>Built-in regular expression engine with no platform dependencies</li>
<li>Built-in Unicode support with no platform dependencies</li>
<li>Built-in number parsing and formatting with no platform dependencies</li>
<li>Additional custom JSON formats (JX and JC)</li>
<li>Bytecode dump/load mechanism for caching compiled functions</li>
</ul>

<h2>Goals</h2>

<p><b>Compliance</b>.  Ecmascript E5/E5.1 and real world compliance.
Ecmascript compliance requires regular expression and Unicode support.
When possible, implement features from the upcoming Ecmascript E6
specification to minimize Duktape custom features.</p>

<p><b>Portability</b>.  Minimal system dependencies are nice when porting,
so Duktape depends on very few system libraries.  For example, number
formatting and parsing, regular expressions, and Unicode are all implemented
internally by Duktape.  One of the few dependencies that cannot be fully
eliminated is system date/time integration in the <code>Date</code> built-in.
Duktape supports major platforms directly but you can also use an external
Date provider on exotic platforms.</p>

<p><b>Easy C interface</b>.  The interface between Duktape and C programs
should be natural and error-tolerant.  As a particular issue, string
representation should be UTF-8 with automatic NUL terminators to match
common C use.</p>

<p><b>Small footprint</b>.  Code and data footprint should be as small as
possible, even for small programs.  Duktape is portable even to "bare metal"
targets with no standard libraries.  This is more important than performance,
as there are already several very fast engines but fewer very compact,
portable engines.</p>

<p><b>Reasonable performance</b>.  Small footprint (and portability, to some
extent) probably eliminates the possibility of a competitive JIT-based engine,
so there is no practical way of competing with very advanced JIT-based engines
like SpiderMonkey (and its optimized variants) or Google V8.  Performance
should still be reasonable for typical embedded programs.
<a href="http://www.lua.org/">Lua</a> is a good benchmark in this respect.
(Adding optional, modular support for JITing or perhaps off-line compilation
would be nice.)</p>

<p><b>ASCII string performance</b>.  It's important that operations dealing
with plain ASCII strings be very fast: ASCII dominates most embedded use.
Operations dealing with non-ASCII strings need to perform reasonably but are
not critical.  This is a necessary trade-off: using C-compatible strings means
essentially using UTF-8 string representation which makes string indexing and
many other operations slower than with fixed size character representations.
It's still important to support common idioms like iterating strings sequentially
(in either direction) efficiently.</p>

<h2>Document organization</h2>

<p><a href="#gettingstarted">Getting started</a> guides you through downloading,
compiling, and integrating Duktape into your program.  It also provides concrete
examples of how you can integrate scripting capabilities into your program.</p>

<p><a href="#programming">Programming model</a>, <a href="#stacktypes">Stack types</a>,
and <a href="#ctypes">C types</a>
discuss core Duktape concepts such as <i>heap</i>, <i>context</i>, <i>value stacks</i>,
<i>Duktape API</i>, and <i>Duktape/C functions</i>.  Duktape stack types and C type
wrappers are discussed in detail.</p>

<p>Duktape specific Ecmascript features are discussed in multiple sections:
<a href="#typealgorithms">Type algorithms</a> (for custom types),
<a href="#duktapebuiltins">Duktape built-ins</a> (additional built-ins), 
<a href="#es6features">Ecmascript E6 features</a> (features borrowed from ES6),
<a href="#custombehavior">Custom behavior</a> (behavior differing from standard),
<a href="#customjson">Custom JSON formats</a>,
<a href="#customdirectives">Custom directives</a>,
<a href="#bufferobjects">Buffer objects</a>,
<a href="#errorobjects">Error objects</a> (properties and traceback support),
<a href="#functionobjects">Function objects</a> (properties),
<a href="#datetime">Date and time</a>,
<a href="#debugger">Debugger</a>,
<a href="#modules">Modules</a>,
<a href="#finalization">Finalization</a>,
<a href="#coroutines">Coroutines</a>,
<a href="#virtualproperties">Virtual properties</a>,
<a href="#internalproperties">Internal properties</a>,
<a href="#bytecodedumpload">Bytecode dump/load</a>,
<a href="#threading">Threading</a>,
<a href="#sandboxing">Sandboxing</a>.
</p>

<p><a href="#performance">Performance</a> provides a few Duktape-specific tips
for improving performance and avoiding performance pitfalls.
<a href="#memoryusage">Memory usage</a> summarizes Duktape memory usage and
gives pointers for minimizing it.
<a href="#compiling">Compiling</a> describes how to compile Duktape in detail,
covering in particular available feature defines.
<a href="#portability">Portability</a> covers platform and compiler specific
issues and other portability issues.
<a href="#compatibility">Compatibility</a> discusses Duktape's compatibility
with Ecmascript dialects, extensions, and frameworks.
<a href="#versioning">Versioning</a> describes Duktape versioning and what version
compatibility to expect.
<a href="#limitations">Limitations</a> summarizes currently known limitations
and provides possible workarounds.</p>

<p><a href="#comparisontolua">Comparison to Lua</a> discusses some differences
between Lua and Duktape; it may be useful reading if you're already familiar with Lua.</p>

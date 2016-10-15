# Duktape 集成


*译文作者：[Atypiape][atypiape]* &nbsp;&nbsp; *原文来自：[Duktape][duktape]* &nbsp;&nbsp; *翻译日期：2016-08-13*

[atypiape]: http://www.atypiape.com
[duktape]: http://www.duktape.org


### 1 参与构建
*************

（可以通过查看[入门指南][getting-started]来了解更详细的介绍）

[getting-started]: http://www.duktape.org/guide.html#gettingstarted

Duktape 的 C 语言源文件和头文件可用于任何构建系统，只需将它们加入到你的项目中参与构建即可。可发行版本中包含一个 Makefile 示例以供参考。以下是个最简单的例子：

```
$ gcc -std=c99 -o test test.c duktape.c -lm
$ ./test
Hello world!
```

### 2 初始化一个环境（context）
****************************

在你程序的任何地方初始化并使用 Duktape：

```
/* test.c */
#include "duktape.h"
int main(int argc, char *argv[]) {
  duk_context *ctx = duk_create_heap_default();
  duk_eval_string(ctx, "print('Hello world!');");
  duk_destroy_heap(ctx);
  return 0;
}
```


### 3 添加 C 函数绑定
********************

想由 ECMAScript 代码来调用一个 C 函数，首先需要定义你的 C 函数：

```
/* 作为一个嵌入式引擎, Duktape 默认不提供 I/O 绑定. 
 * 以下是个简单的 print() 函数, 带有一个参数.
 */
static duk_ret_t native_print(duk_context *ctx) {
  printf("%s\n", duk_to_string(ctx, 0));
  return 0;  /* 没有返回值 (= undefined) */
}

/* 加法器: 将几个参数值进行相加. */
static duk_ret_t native_adder(duk_context *ctx) {
  int i;
  int n = duk_get_top(ctx);  /* 参数个数 */
  double res = 0.0;

  for (i = 0; i &lt; n; i++) {
    res += duk_to_number(ctx, i);
  }

  duk_push_number(ctx, res);
  return 1;  /* 一个返回值 */
}
``` 

接下来，注册你这个函数，比例将它注册到全局对象中：

```
duk_push_c_function(ctx, native_print, 1 /*nargs*/);
duk_put_global_string(ctx, "print");
duk_push_c_function(ctx, native_adder, DUK_VARARGS);
duk_put_global_string(ctx, "adder");
```

然后，你就可以在 ECMAScript 代码中调用你的函数了：

```
duk_eval_string_noresult(ctx, "print('2+3=' + adder(2, 3));");
```

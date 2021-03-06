---
title: 字符串操作火箭炮——正则表达式
description: 重新认识正则表达式的强大。
date: 2017-08-03 22:04:37
tags: [正则表达式, 前端]
category: 学习
---

正则表达式是一种描述字符串匹配的模式，我们通过构建正则表达式来匹配满足规则的字符串。正则表达式常用于对字符串进行检测，替换，提取，搜索。<!-- more -->


## API

正则表达式相关API。

### 正则实例

```
// 构造函数

var re = new RegExp('abc');

// 字面量

var re = /abc/;
```

### 正则方法

正则方法指，正则对象上的方法，即RegExp构造函数生成的实例可以调用的方法。

1. test方法

检测字符串内是否包含有正则所匹配的字符子串。
返回一个布尔值。

```
// re.test(str)

var re = /abc/;

console.log(re.test('abc')); // true
```


### 正则相关方法

正则相关方法，指可以使用一个正则实例作为参数去调用的方法。

1. match方法

### 正则修饰符

正则修饰符用于限制或者增强正则表达式的匹配能力。
它有如下2种用法：

```
// 1. 作为参数 传给正则构造函数

var re = new RegExp('abc', 'g');

// 2. 写在正则字面量后

var re = /abc/g;
```

上面的2个正则匹配效果是相同的。

常用的正则修饰符有:

- g **global** 代表匹配全局所以的满足匹配模式的字符子串。强调"所有"。
- i **ignore** 代表正则匹配的时候忽略字母的大小写




## 匹配攻略

> 正则表达式是匹配模式，要么匹配字符，要么匹配位置。

### 模糊匹配

如果正则只能进行精确匹配，那么它的存在没多大意义。

1. 横向模糊匹配

横向模糊指正则可以匹配的字符串长度可以是不固定的。

实现方式是使用量词，表示可能出现的次数范围。

```
var re = /ab{2,5}c/g,
    str = 'abc abbc abbbc abbbbc';

console.log(str.match(re)); // ['abbc', 'abbbc', 'abbbbc']
```

2. 纵向模糊匹配

纵向模糊指一个正则实例匹配的字符串，具体到某一位字符时候，它可以是不确定的，有多中可能。

实现方式是使用字符组，表示可以是组内的任一个。

```
var re = /a[123]b/g,
    str = 'a1b a2b a3b a4b';

console.log(str.match(re)); // ['a1b', 'a2b', 'a3b']
```

### 字符组

1. 范围表示法

如果字符组里面的字符特别多的话，用范围表示法会更方便。

比如`[2345678abcdefgUVWXYZ]`，可以这么表示`[2-8a-gU-Z]`。

**-**是连字符，表示字符范围。

而如果我们要匹配的字符里面包括它，我们可以使用反斜杠来转义，或者避免将其放在2个字符中间。

如要匹配`a-z`3个字符：

```
var re = /[-az]/;

var re = /[az-]/;

var re = /[a\-z]/;
```

2. 排除字符组

纵向匹配有一种情况，就是某位字符不匹配某些字符。这时候我们就可以使用脱字符**^**。表示求反，排除匹配的字符。

比如`/[^abc]/`可以匹配除了a，b，c以外的任何单字符。

3. 常用简写

简写是语法提供给我们的一些字符组的符号表示。

```
\d 代表[0-9] 表示一位数字 d是digit
\D 代表[^0-9] 表示除数字外任意字符

\w 代表[0-9a-zA-Z_] 表示数字、大小写字母和下划线 w是word
\W 代表[^0-9a-zA-Z_] 表示非数字，字母和下划线外的一个任意字符。

\s 代表[\t\v\n\r\f] 表示空白符 包括空格，水平制表符，竖直制表符，换行符，回车符，插页符 s是space
\S 代表非空白符

. 代表[^\n\r\u2028\u2029] 通配符，表示几乎任意字符。 换行，回车，行分隔符和段分割符除外。

// 匹配任意字符方式 [\d\D] [\w\D] [\s\S] [^]。
```

### 量词

量词代表对重复次数范围的限制。

1. 简写形式:

  - {m,} 表示至少出现m次
  - {m} 表示正好出现m次
  - {m,n} 表示出现m次至n次 m和n都包括
  - ? 代表出现1次或者0次 等价于{0,1}
  - + 代表出现过至少一次 等价于{1,}
  - * 代表出现过任意次，可能不出现 等价于{0,}

2. 贪婪匹配和惰性匹配

```
// 贪婪匹配

var re = /\d{2,5}/g,
    str = '123 1234 12345 1234567';

console.log(str.match(re)); // ['123', '1234', '12345', '1234567']
```

贪婪匹配指，正则会尽可能多的匹配。比如我们给的范围是`2-5`，那么它会匹配的长度就是尽量匹配5个字符，如果不能匹配5个，再考虑4个字符。如果有6个满足的字符，它会匹配前5个。

```
// 惰性匹配

var re = /\d{2,5}?/g,
    str = '123 1234 12345 123456';

console.log(str.match(re)); // ['12', '12', '34', '12', '34', '12', '34', '56']
```

惰性匹配模式下，它会尽可能少的匹配，比如我们定义匹配`2-5`个字符长度，如果匹配到了2个长度字符，它就会停止这次匹配。]

通常我们通过在量词后加问好来实现惰性匹配。

比如:

```
{m,n}?  {m,}?   ??  +?  *?
```

### 多选分支

一个模式可以支持横向模糊或者纵向模糊。而多选分支则可以支持多个子分支支持其一。

实现多选分支，需要用到管道符**|**。

```
var re = /good|nice/g,
    str = 'good job, nice work';

console.log(str.match(re)); // ['good', 'nice']
```

但是需要注意的是比如用`/good|goodbye/`去匹配`goodbye`的时候，得到的是good。这是因为多选分支是惰性匹配的，前者匹配到了结果后者就不继续匹配了。要匹配得到goodbye可以使用`/goodbye|good/`。


## 参考资料

[正则表达式系列总结](https://zhuanlan.zhihu.com/p/27653434)

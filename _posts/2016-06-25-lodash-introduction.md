---
layout: post
title: Lodash introduction
author: xszhi
categories: tech-note
tags: [lodash, util, functional programming]
---
npm 上下载量最大的库，有着极佳的运行效率与非常优雅的接口设计，提供了很多 es6 之前未能提供的 util function. [lodash](https://lodash.com/docs#template)

<!-- more -->

# lodash introduction

运行快，在 js 语境下更好的[函数式](https://github.com/lodash/lodash/wiki/FP-Guide)

> The lodash/fp module promotes a more functional programming (FP) friendly style by exporting an instance of lodash with its methods wrapped to produce immutable auto-curried iteratee-first data-last methods.

`` fp =

## 1.installation

各种模块类型全面支持

**1. 浏览器**

```html
<script src="lodash.js"></script>
```

**2. cmd**

```bash
$ npm i --save lodash
```

```js
// Load the full build.
var _ = require('lodash');
// Load a method category.
var array = require('lodash/array');
var object = require('lodash/fp/object');
```

## 2.basic usage

```js
_.assign({ 'a': 1 }, { 'b': 2 }, { 'c': 3 });
// ➜ { 'a': 1, 'b': 2, 'c': 3 }
_.map([1, 2, 3], function(n) { return n * 3; });
// ➜ [3, 6, 9]
```

根据处理对象的不同，lodash 的方法被分为 `Array, Collection, Date, Function, Lang, Math, Number, Object, Seq, String, util, Properties, Methods` 12 个模块。 其中 Properties 和 Methods 是对 template 方法的参数设置（用于字符串模版的渲染，要把这事儿搞得大，可以结合 heredoc 使用）。
对于接口文档中一些不明觉厉的词语， 比如 iteratee, predicate ... 可以通过研究 util 模块研究了解，本文的第三部分也将对常见的接口进行描述。



## 3.chain

## 4.interface

## 5.Recipes

1. 过滤对象的多余属性





















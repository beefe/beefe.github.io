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

运行快，在 js 本身能力有限的情况下提供了大量抽象层次更高的函数。

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

根据处理对象的不同，lodash 的方法被分为 `Array, Collection, Date, Function, Lang, Math, Number, Object, Seq, String, util, Properties, Methods` 12 个模块。 其中 Properties 和 Methods 是对 template 方法的参数设置。

## 3.chain

通过 Seq 模块内方法实现。

通过 `_` 方法，将会创建一个 lodash 对象（可以类比 Jquery 对象理解），对于以数组、函数、组合作为操作对象并返回数组的方法就可以链式调用了。如果一个方法操作或是返回简单值，那么链式调用将被直接中断。同时需要注意的是，在链式调用过程中，操作的结果值是不会主动返回的，如果要返回过程中的值需要显式地调用 `.value()` 方法。当显式地调用 `_.chain` 方法时，值得返回必须使用 `value()`。

链式调用在执行时，将会延迟到需要返回值时。

```js

```

## 4.interface and shorthand

## 5.Recipes

1. 过滤对象的多余属性





















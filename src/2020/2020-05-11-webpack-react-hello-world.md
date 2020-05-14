# 从 `hello world` 示例解析 `webpack` 打包后的 `react` 项目代码运行原理

![](http://www.iseeit.cn/wp-content/uploads/2020/05/download.jpeg)

## 环境

* webpack 4.43.0
* react 0.14.9
* react-dom 0.14.9
* babel-loader: 8.1.0
* @babel/core: 7.9.0

## 先上结论

`webpack` 编译生成的 JavaScript 文件代码基于以下3个核心点：

* 入口模块最外层是一个 `IIFE`（Immediately Invoked Function Expression），立即执行函数，确保入口模块（文件）一旦加载完毕立刻执行初始化；
* `IIFE` 内置一个公共的模块加载函数，用于模块加载（及缓存）；
* 每个模块都是一个函数，加载模块本质其实是执行函数代码并返回其暴露的对象；

所以其核心思想就是，把不同的模块编译为不同的函数（使用函数作用域对代码进行环境隔离），并在需要加载模块的时候执行该模块对应的函数，over。

下文则是对核心代码的抽丝剥茧。

## 入口模块：立即执行函数

删减一些非主干分支代码后，可以看到，入口模块主框架如下：

```
 (function(modules) {
  // 1. 声明模块加载函数 
 	function __webpack_require__(moduleId) {
    ... // 略去若干非主干代码
 		return module.exports;
 	}
  __webpack_require__.* = *;
  ... // 略去若干非主干代码
  // 2. 加载入口模块
  return __webpack_require__(__webpack_require__.s = "./src/index.js");
 })({
   // 3. 传入依赖参数
   "./src/test.js":(function(module, exports, __webpack_require__){ ... // 略去若干非主干代码 }),
    "react": (function(module, exports) { ... // 略去若干非主干代码 }),
    "react-dom": (function(module, exports) { ... // 略去若干非主干代码 }),
 });
```

继续进行精简可以看到如下结构，就是一个立即执行函数嘛，如下：

```
(function(modules) {
  // 函数实体
})({
  // 实参
});
```

## 立即执行函数的实参：普通 JavaScript 对象

示例代码中，实参对象有3个属性，其值都是普通函数：

```
{
  "./src/index.js":(function(module, exports, __webpack_require__){ ... // 略去若干非主干代码 }),
  "react": (function(module, exports) { ... // 略去若干非主干代码 }),
  "react-dom": (function(module, exports) { ... // 略去若干非主干代码 }),
}
```

可以看到依次是：

* 项目入口模块 `./src/index.js` 对应的函数；
* `react` 模块对应的函数；
* `react-dom` 模块对应的函数；

## 立即执行函数内部逻辑：定义模块加载函数并默认加载入口模块

立即执行函数内的执行过程主要分两步：

1. 定义模块加载函数；
2. 加载初始模块；

首先，定义模块加载函数 `__webpack_require__`：

```
var installedModules = {}; // 用于缓存已加载模块
function __webpack_require__(moduleId) {
  // 检查模块是否已经被缓存，如已缓存直接返回其结果即可
  if(installedModules[moduleId]) {
    return installedModules[moduleId].exports;
  }
  // 创建模块，并将该模块放进缓存
  var module = installedModules[moduleId] = {
    i: moduleId,
    l: false, // 模块是否已加载就绪
    exports: {} // 模块内容
  };

  // 执行模块函数，对模块进行初始化，这里的 modules 是最外层立即执行函数的参数
  modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

  // 标记该模块已加载就绪
  module.l = true;

  // 返回当前模块的实体
  return module.exports;
}
```

其次，初始化，加载入口模块：

```
 (function(modules) {
   function __webpack_require__(moduleId){}

   return __webpack_require__('./src/index.js'); // 加载入口模块
 })({
   "./src/index.js": (function(){}),
   ...
 })
```

入口模块（包含初始 `DOM` 的创建代码）加载之后，页面初始化完成，如下代码示例：

```
"./src/index.js": (function(){
  "use strict";
  eval(
    ... // 略去非主干代码
    \n\nvar Test = function Test() {\n  return /*#__PURE__*/_react[\"default\"].createElement(\"div\", null, \"hello world\");\n};\n\n_reactDom[\"default\"].render( /*#__PURE__*/_react[\"default\"].createElement(Test, null), document.getElementById(\"app\"));
  );
}),
```

实质就是:

1. 调用 `react` 的 `createElement` 方法创建 `DOM`;
2. 调用 `react-dom` 的 `render` 方法渲染上一步创建的 `DOM`;


## 总结

`webpack`（借助 `babel`） 打包后的 `react` Web 项目代码有以下特性：

1. 入口模块最外层是一个立即执行函数，确保入口模块（文件）一旦加载完毕立刻执行初始化；
2. 立即执行函数内置一个公共的模块加载函数，用于模块加载（及缓存）；
3. 每个模块都是一个函数，加载模块本质就是执行函数代码并返回其暴露的对象；

> `webpack`(借助 `babel`)编译的本质就是把各种模块编译为普通函数并执行。（👍，真是简单而又灵动啊）

本文首发于 [ http://www.iseeit.cn/index.php/2020/05/14/webpack-react-babel-hello-world/]( http://www.iseeit.cn/index.php/2020/05/14/webpack-react-babel-hello-world/)。

以上。

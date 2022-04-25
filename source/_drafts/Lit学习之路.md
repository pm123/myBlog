---
title: Lit学习之路
tags:
- web components
- Lit
categories: 前端
---

开篇首先介始需要一下web components的背景：组件化已经成为目前主流的前端开发方式，但是我们实现组件化主要依托于vue, angular, react等市面上流行的前端框架，这些框架在遵循W3C的规则下也有一套自己的开发规则和书写语法，官方也推出一套组件化的解决方案和原生API的支持——web components。

优势：

1. 可以直接当做原生标签，在任何前端框架和无框架中运行。
2.  CSS 防污染
3. 浏览器原生支持的组件化实现

Web components 原生对css 的隔离性，从另外一个角度来说也是它的缺点之一。web components开发的组件，在使用时不可避免地需要改变它的内部样式，我认为有两种方法可以有效地达到效果：

[使用教程--张鑫旭]: https://www.zhangxinxu.com/wordpress/2021/02/css-part-shadow-dom/



1. css变量
2. ::part伪元素





## 组件

Lit 组件是可重用的UI片段。您可以将 Lit 组件视为具有某种状态并根据其状态显示 UI 的容器。它还可以对用户输入、触发事件做出反应——任何你希望UI组件做的事情。Lit 组件是一个HTML元素，因此它具有所有标准元素API。

创建一个Lit 组件涉及以下几个概念：

- Defining a component. Lit 组件作为自定义元素实现，并在浏览器中注册。
- Rending. 组件包含 render 方法，用来渲染组件的内容。在render方法中，您为组件定义了一个模板。
- Reactive properties. 属性保存组件的状态。改变组件的一个或多个响应式属性会触发更新周期，重新渲染组件。
- Styles. 组件可以定义封装样式来控制自己的外观。
- Lifecycle. Lit定义了一组回调函数，您可以覆盖这些回调以挂载到组件的生命周期。例如，元素添加到页面、组件更新都会运行这些回调。


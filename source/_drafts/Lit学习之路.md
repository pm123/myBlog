---
title: Lit学习之路
tags:
- web components
- Lit
categories: 前端
---

## 组件

Lit 组件是可重用的UI片段。您可以将 Lit 组件视为具有某种状态并根据其状态显示 UI 的容器。它还可以对用户输入、触发事件做出反应——任何你希望UI组件做的事情。Lit 组件是一个HTML元素，因此它具有所有标准元素API。

创建一个Lit 组件涉及以下几个概念：

- Defining a component. Lit 组件作为自定义元素实现，并在浏览器中注册。
- Rending. 组件包含 render 方法，用来渲染组件的内容。在render方法中，您为组件定义了一个模板。
- Reactive properties. 属性保存组件的状态。改变组件的一个或多个响应式属性会触发更新周期，重新渲染组件。
- Styles. 组件可以定义封装样式来控制自己的外观。
- Lifecycle. Lit定义了一组回调函数，您可以覆盖这些回调以挂载到组件的生命周期。例如，元素添加到页面、组件更新都会运行这些回调。


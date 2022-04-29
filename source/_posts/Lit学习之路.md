---
title: Lit学习之路
tags:
  - web components
  - Lit
categories: 前端
date: 2022-04-29 14:10:26
---


开篇首先介始一下web components的背景：组件化已经成为目前主流的前端开发方式，但是我们实现组件化主要依托于vue, angular, react等市面上流行的前端框架，这些框架在遵循W3C的规则下也有一套自己的开发规则和书写语法，官方也推出一套组件化的解决方案和原生API的支持——web components。

优势：

1. 可以直接当做原生标签，在任何前端框架和无框架中运行。

2. CSS 隔离

   > *Shadow DOM 中定义的 CSS 样式只在 ShadowRoot 下生效。这意味着样式被封装了起来。* 

3. 浏览器原生支持的组件化实现

Web components 原生对css 的隔离性，从另外一个角度来说也是它的缺点之一。web components开发的组件，在使用时不可避免地需要改变它的内部样式，我认为有两种方法可以有效地达到效果：

1. css变量
2. ::part伪元素

其中，`<slot>`元素作为占位元素，具体的内容显示是在父元素中的子元素决定的，它的样式有哪些方法可以改变呢？

```html
<my-element>
    <span>子元素</span>
</my-element>

<template>
  	<p>Welcome to the web components tutorial!</p>
    <p>This is the custom code.</p>
    <slot part="slot">this is slot</slot>
</template>
```

```css
/* 直接设置子元素的样式 */
my-element > span {
  color: blue;
}
/* 使用::part设置slot元素的样式，<span>元素作为<slot>的子元素，继承其样式 */
zxx-info::part(slot) {
  border-bottom: 1px dashed;
}
```

在使用原生的web components写ui时，会有一种回到原生 JS 写应用的时代，特别是写事件回调时，需要在custom element 内部自定义事件 `new CustomEvent()`，外部用 `addEventListener`监听。这可能会让习惯使用了vue，react等框架的开发同学不太适应，因些采用原生web components 与 Lit 对比的方法，介绍web components 的简单用法；

## Web components库——Lit

Lit 组件是可重用的UI片段。您可以将 Lit 组件视为具有某种状态并根据其状态显示 UI 的容器。它还可以对用户输入、触发事件做出反应——任何你希望UI组件做的事情。Lit 组件是一个HTML元素，因此它具有所有标准元素API。

创建一个Lit 组件涉及以下几个概念：

- Defining a component. Lit 组件作为自定义元素实现，并在浏览器中注册。

- Rending. 组件包含 render 方法，用来渲染组件的内容。在render方法中，您为组件定义了一个模板(类似于jsx的语法)。

- Reactive properties. 属性保存组件的状态。改变组件的一个或多个响应式属性会触发更新周期，重新渲染组件。

- Styles. 组件可以定义封装样式来控制自己的外观。

- Lifecycle. Lit定义了一组回调函数，您可以覆盖这些回调以挂载到组件的生命周期。例如，元素添加到页面、组件更新都会运行这些回调。

  Tip: 装饰器是一项提议的 JavaScript 功能，因此需要使用 Babel 或 TypeScript 之类的编译器来使用装饰器。

```typescript
import {LitElement, css, html} from 'lit';
import {customElement, property} from 'lit/decorators.js';

@customElement('simple-greeting')
export class SimpleGreeting extends LitElement {
  // Define scoped styles right with your component, in plain CSS
  static styles = css`
    :host {
      color: blue;
    }
  `;

  // Declare reactive properties
  @property()
  name?: string = 'World';

  // Render the UI as a function of component state
  render() {
    return html`
				<p>Hello, ${this.name}!</p>
				<p><button @click="${this._increment}">Click Me!</button></p>
		`;
  }
  // click 事件回调
  @eventOptions()
  private _increment(e: Event) {
    this.count++;
  }
}
```

上面是使用Lit声明的自定义元素，与原生的web components 相比，render、响应式属性等功能，把我们从手写原生js的泥沼中拉了出来。毕竟习惯使用vue, react等声明式框架写ui后，重新回到写类似写jQuery(命令式框架)或原生js的时代，一时会有点很难适应。

##### Events

除了标准的 addEventListener API 之外，Lit 还引入了一种声明式的方式来添加事件监听器。

Lit  使用`@eventOptions` 装饰器在侦听器上(`_handleTouchStart`)指定事件选项，传递给`@eventOptions` 的对象作为选项参数传递给`addEventListener`。参考上一个例子

首先看一下使用原生web components添加自定义事件：

```javascript
constructor(){
   super();
   this._shadowRoot = this.attachShadow({mode: 'open'});
   this.checkEvent = new CustomEvent("check", {
     bubbles: true,
     cancelable: false,
     composed: true,
   });
 }

connectedCallback(){
  ...
  this.shadowRoot.addEventListener("check", function (e) {
        console.log('listend to check event');
        console.log(e);
    });
 }
```

Lit

```typescript
const event = new Event('my-event', {bubbles: true, composed: true}); // 也可以使用CustomEvent
myElement.dispatchEvent(event);
```

那么事件在Shadow DOM中是如何工作的呢？

使用 shadow DOM 时，需要对标准事件系统进行一些重要的修改。默认情况下，shadow dom内的事件调度在shadow dom外是不可见的。为了能够在外部触发事件，需要设置`eventOptions`的`composed`为`true`，同时event 的 target 指向lit 组件本身，而不是其shadow dom中的子元素。当然，如果需要获取事件的具体target，可以使用`event.composedPath() `API， 这个方法返回事件调度遍历的所有节点的数组，包括shadow DOM中的节点。

##### Lifecycle

| **生命周期**             | Lit                                                          | Web components                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------------- |
| connectedCallback        | 当Lit组件连接到文档的 DOM 时，将会初始化第一个元素更新周期；在准备渲染时，Lit 还要确保创建了 renderRoot（通常是它的 shadowRoot）。 | 当自定义元素第一次被连接到文档DOM时被调用。        |
| disconnectedCallback     | 暂停[reactive update cycle](https://lit.dev/docs/components/lifecycle/#reactive-update-cycle)。重连后恢复 | 当自定义元素与文档DOM断开连接时被调用。            |
| adoptedCallback          | Lit 对此回调没有默认行为。                                   | 当自定义元素被移动到新文档时被调用。               |
| attributeChangedCallback | Lit 使用此回调将属性attribute的更改同步到reactive properties。Lit 还会自动设置元素的observedAttributes 数组以匹配组件的reactive properties列表。 | 当自定义元素的一个属性被增加、移除或更改时被调用。 |

除了标准的自定义元素生命周期之外，Lit 组件还实现了reactive update cycle。当响应属性更改或显式调用 requestUpdate() 方法时，将触发响应更新周期。更新发生在微任务时间，这意味着它们发生在浏览器将下一帧绘制到屏幕之前。

概括地说，reactive update cycle是：

1. 当一个或多个属性更改或调用 requestUpdate() 时会安排触发更新；
2. 在绘制下一帧之前执行更新：设置反射属性 和 调用组件的 render 方法来更新其内部 DOM；
3. The update is completed and the `updateComplete` promise is resolved.

由于更新是异步执行的，因此在执行更新之前发生的任何和所有更改都只会导致一次更新。

### React 与 Web components

> React and [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) are built to solve different problems. Web Components provide strong encapsulation for reusable components, while React provides a declarative library that keeps the DOM in sync with your data. The two goals are complementary. As a developer, you are free to use React in your Web Components, or to use Web Components in React, or both.

### Vue 与 Web components

> 我们认为 Vue 和 Web Components 大体上是互补的技术。Vue 能很好地解析和创建自定义元素。不论是在将自定义元素整合到已有的 Vue 应用中，还是使用 Vue 构建和分发自定义元素，你都能获得很好的支持。

参考资料：

【1】https://www.zhangxinxu.com/wordpress/2021/02/css-part-shadow-dom/

【2】https://developer.mozilla.org/zh-CN/docs/Web/Web_Components

【3】https://lit.dev/docs/

【4】https://www.html5rocks.com/zh/tutorials/webcomponents/shadowdom-201/

【5】https://custom-elements-everywhere.com/
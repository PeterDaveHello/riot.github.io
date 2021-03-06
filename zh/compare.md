---
layout: zh
title: Riot 与 React 和 Polymer 的比较
---

# **Riot** vs **React** & **Polymer**

以及Riot与这些兄弟库之间的差异。

## React

Riot 2 受到 React 和 “内聚” 思想的启发。引用 Facebook 开发人员的话:

> "模板分离的是技术，而不是关注点."

我们对此见解表示致敬。目标应该是构建可重用的组件而不是模板. 将逻辑从模板中分离出来，实际上我们是把本该在一起的东西拆开了。

通过在同一个组件中将这些相关的技术组合起来，系统变得更干净。我们要表达对 React 的敬意，因为它体现了超凡的洞察力。

我们用 React 用得挺好的。在 [Disqus Importer](/importer/) 中我们仍然在使用它。但我们被React的语法和文件大小 (*特别是* 语法)所困扰. 我们开始思考事情可能可以更简单；无论是内部实现还是对用户的接口。


### React 语法

以下示例直接引用自 React 首页:


```js
var TodoList = React.createClass({
  render: function() {
    var createItem = function(itemText) {
      return <li>{itemText}</li>;
    };
    return <ul>{this.props.items.map(createItem)}</ul>;
  }
});
var TodoApp = React.createClass({
  getInitialState: function() {
    return {items: [], text: ''};
  },
  onChange: function(e) {
    this.setState({text: e.target.value});
  },
  handleSubmit: function(e) {
    e.preventDefault();
    var nextItems = this.state.items.concat([this.state.text]);
    var nextText = '';
    this.setState({items: nextItems, text: nextText});
  },
  render: function() {
    return (
      <div>
        <h3>TODO</h3>
        <TodoList items={this.state.items} />
        <form onSubmit={this.handleSubmit}>
          <input onChange={this.onChange} value={this.state.text} />
          <button>{'Add #' + (this.state.items.length + 1)}</button>
        </form>
      </div>
    );
  }
});

React.render(<TodoApp />, mountNode);
```

JSX 是 HTML 和 JavaScript 的混合体. 在组件中你可以在各处包含HTML; 在方法内部及属性赋值中.


### Riot 语法

上例用 Riot 写是这样的:

``` html
<todo>
  <h3>TODO</h3>

  <ul>
    <li each={ item, i in items }>{ item }</li>
  </ul>

  <form onsubmit={ handleSubmit }>
    <input>
    <button>Add #{ items.length + 1 }</button>
  </form>

  this.items = []

  handleSubmit(e) {
    var input = e.target[0]
    this.items.push(input.value)
    input.value = ''
  }
</todo>
```

上面的标签这样加载到页面上:

```js
<todo></todo>

<script>riot.mount('todo')</script>
```

### 相同，又不同

在 Riot 中先有 HTML ，然后才是JavaScript . 两者放在同一个组件中，但清晰地与彼此分离开。HTML 中可以加入 JavaScript 表达式.

除了在花括号里写表达式的写法，没有专有的东西.

几乎看不到什么脚手架（boilerplate）代码. 更少的符号，标点，系统属性和方法名. 字符串可以做插值: `"Hello {world}"` 替代 `"Hello " + this.state.world` ，方法可以用简洁的 ES6 语法来写。所有的东西都变少了.

我们认为 Riot 的语法是分离布局和逻辑，又能享受独立的组件带来的好处的最干净的方式。


### 基于字符串String vs 基于 DOM

组件初始化的过程中，React 解析字符串，而 Riot 遍历 DOM 树.

Riot 从DOM树中提取表达式，将它们保存在一个数组里。每个表达式拥有一个指向DOM结点的指针。每次更新，这些表达式被重新求值并与DOM中的旧值进行比较。如果有值发生了变化，相应的DOM结点也被更新。这样，Riot也有了一个虚拟DOM，只是是非常简单的一个。

由于这些表达式可以被缓存，更新周期是非常快的. 遍历 100 到 1000 个表达式通常只需要不到 1ms.

React 的同步算法要复杂得多得多，因为HTML布局在每次更新后会随机变化. 这项挑战巨大，所以必须为 Facebook 开发人员的优秀成果点一下赞.

我们看到这些复杂的比较是可以被避免的。

在 Riot 中 HTML 的结构是固定的。只有循环和条件能够增加或删除元素。例如，一个 `div` 不能变成一个 `label` . Riot 不做复杂的子树替换，而只是更新表达式的值。


### Flux 和路由

React 只搞UI, 这是正确的. 所有伟大的软件项目都专注于一个点。

Facebook 建议使用 [Flux](http://facebook.github.io/flux/docs/overview.html) 来管理前端代码的结构。它不象一个框架而更象一种模式，里面有很多好的想法.

Riot 提供了自定义标签，事件触发器 (observable) 和路由功能. 我们相信这些是前端应用的基本组成单元，自定义标签用于用户界面, 事件用于模块化，路由用于 URL 管理和回退按钮支持.

象 Flux 一样, Riot 非常灵活，将更大的架构决策留给开发人员去做。它自己只是一个帮助你完成目标的工具库。

你可以使用Riot的observable和路由器构造一个类似 Flux 的系统. 事实上这东西已经 [有了](https://github.com/jimsparkman/RiotControl).


### 大小相差 {{ site.react.size | divided_by: site.size_min | round: 0 }}倍 - {{ site.react_router.size | divided_by: site.riot_route_size_min | round: 0 }}倍

React (v{{ site.react.version }}) 比 Riot 大 {{ site.react.size | divided_by: site.size_min | round: 0 }} 倍

<small><em>react.min.js</em> – {{ site.react.size }}KB</small>
<span class="bar red"></span>

<small><em>riot.min.js</em> – 13.21KB</small>
<span class="bar blue" style="width: {{ site.size_min | divided_by: site.react.size | times: 100 }}%"></span>

<br>

推荐的 React 路由器 (v{{ site.react_router.version }}) 比 Riot 路由器大 {{ site.react_router.size | divided_by: site.riot_route_size_min | round: 0 }} 倍 .

<small><em>react-router.min.js</em> – {{ site.react_router.size }}KB</small>
<span class="bar red"></span>

<small><em>react-mini-router.min.js</em> – {{ site.react_mini_router.size }}KB</small>
<span class="bar red" style="width: {{ site.react_mini_router.size | divided_by: site.react_router.size | times: 100 }}%"></span>

<small><em>riot.route.min.js</em> – {{ site.riot_route_size_min }}KB</small>
<span class="bar blue" style="width: {{ site.riot_route_size_min | divided_by: site.react_router.size | times:100 }}%"></span>

必须承认，这个路由器比较是不公平的，因为 [react-router](https://github.com/rackt/react-router) 功能强大得多. 但上面的图表清晰地体现了Riot的目标: 只提供最少量的API.

React 的生态系统更框架化，倾向于庞大的 API 接口. 在React社区里上面这个大路由器比 [react-mini-router](https://github.com/larrymyers/react-mini-router) 更受欢迎。


## Polymer

Polymer 使用 Web Component 标准，使它在最新的浏览器上可用. 这使得你可以以标准化的方式编写自定义标签。

从概念上看 Riot 的目标是一样的，但也存在区别:

1. Web Component 语法是实验性的，比较复杂

2. Riot 只更新变化了的元素，因此DOM操作更少

3. 组件是通过 HTML `link rel="import"` 载入的. 必须使用 Polyfill 来对 XHR 进行排队, 非常的慢. Riot 标签用 `script src` 载入，多个标签可以用常用的工具来进行合并。

4. Polymer 使用双向数据绑定，而 riot 是单向绑定.

5. Polymer不支持服务端渲染。


### 大小相差 {{ site.polymer_and_webcomponents_size | divided_by: site.size_min | round: 0 }} 倍

Polymer (v{{ site.polymer.version }}) + WebComponents(v{{ site.webcomponents.version }}) 比 Riot 大 {{ site.polymer_and_webcomponents_size | divided_by: site.size_min | round: 0 }} 倍

<small><em>polymer.min.js</em> – {{ site.polymer.size }}KB</small>
<span class="bar red"></span>

<small><em>riot.min.js</em> – <span class="riot-size">{{ site.size_min }}KB</span></small>
<span class="bar blue" style="width: {{ site.size_min | divided_by: site.polymer.size | times: 100 }}%"></span>

Web components 被称为 [polyfill之王](http://developer.telerik.com/featured/web-components-arent-ready-production-yet/) ，这就是为什么 Polymer的实现需要这么多代码。


### Web Componnets

Web Component 是标准，所以会是组件技术的最终方向。最终互联网上将全部是这种标准组件，但这可能需要很长[时间](http://caniuse.com/#search=web%20components)

由于其中涉及的复杂性，有很大的机率这些组件并不是被直接使用。在web component上面将会有新的层次。就象目前我们有jQuery一样。大部分开发者并不是直接使用DOM API.

Riot就是这样一种抽象。它提供了应用可以一直使用的非常易用的API。一旦 web component 规范发生了进化，并带来了真实的好处，如性能上的提升，Riot可以 *在内部* 使用它。

Riot的目标是使 UI 开发尽可能地简单。当前的 API 。可以把它理解成 "web component的jquery" - 它提供了达成同样目标的更简短的语法。它简化了编写可重用组件的整体开发体验。

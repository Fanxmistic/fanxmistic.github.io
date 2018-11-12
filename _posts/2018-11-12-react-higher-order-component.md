---
layout: post
title: "React 高阶组件"
date: 2018-11-12
categories: React
cover: "/assets/img/react-high.png"
tags: React JavaScript 前端
---

> 开始之前，有两点需要我们注意一下：1、React 高阶组件 仅仅是一种模式，并不是 React 的基础重要知识；2、它不是开发 React app 的必要知识。你可以略过此文章，任然可以开发 React app。然而，就像其他事物一样，你对其越熟悉，才会有更好的收获。如果你写 React app，不掌握它将成为你的遗憾。

如果你不知道 `Don't Repeat Yourself` 或 `D.R.Y`，那么在软件开发中必定走不太远。对于大多数开发者来说，它是一个值得长期努力的目标。在这篇文章当中，我们将了解到如何在 React 当中运用 `DRY` 原则 —— 高阶组件。开始阐述之前，我们先来认识一下问题所在。

假设我们要开发类似 `Stripe` 的主页面。正如大多的项目一样，我们先按部就班地开发着。当开发到差不多的时候，你会发现主页面上有很多当鼠标悬浮在某个元素上时出现 `tooltip` 的场景。

![图片](/assets/img/tool-tips.gif)

有很多种方法做到这样。你可能想到写一个带悬浮状态的组件来控制 `tooltip` 的显示与否。那么你需要添加三个组件——Info, TrendChart 和 DailyChart。

我们从 Info 组件开始。它很简单，仅仅是一个 `SVG icon`.

```javascript
class Info extends React.Component {
  render() {
    return (
      <svg
        className="Icon-svg Icon--hoverable-svg"
        height={this.props.height}
        viewBox="0 0 16 16"
        width="16"
      >
        <path d="M9 8a1 1 0 0 0-1-1H5.5a1 1 0 1 0 0 2H7v4a1 1 0 0 0 2 0zM4 0h8a4 4 0 0 1 4 4v8a4 4 0 0 1-4 4H4a4 4 0 0 1-4-4V4a4 4 0 0 1 4-4zm4 5.5a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3z" />
      </svg>
    );
  }
}
```

然后我们需要添加一个状态来记录组件是否被 Hover，可以用 React 鼠标事件当中的 `onMouseOver` 和 `onMouseOut`来实现。

```javascript
class Info extends React.Component {
  state = { hovering: false };
  mouseOver = () => this.setState({ hovering: true });
  mouseOut = () => this.setState({ hovering: false });
  render() {
    return (
      <>
        {this.state.hovering === true ? <Tooltip id={this.props.id} /> : null}
        <svg
          onMouseOver={this.mouseOver}
          onMouseOut={this.mouseOut}
          className="Icon-svg Icon--hoverable-svg"
          height={this.props.height}
          viewBox="0 0 16 16"
          width="16"
        >
          <path d="M9 8a1 1 0 0 0-1-1H5.5a1 1 0 1 0 0 2H7v4a1 1 0 0 0 2 0zM4 0h8a4 4 0 0 1 4 4v8a4 4 0 0 1-4 4H4a4 4 0 0 1-4-4V4a4 4 0 0 1 4-4zm4 5.5a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3z" />
        </svg>
      </>
    );
  }
}
```

看起来还不错，我们需要在 `TrendChart` 和 `DailyChart`写同样的逻辑。

```javascript
class TrendChart extends React.Component {
  state = { hovering: false };
  mouseOver = () => this.setState({ hovering: true });
  mouseOut = () => this.setState({ hovering: false });
  render() {
    return (
      <>
        {this.state.hovering === true ? <Tooltip id={this.props.id} /> : null}
        <Chart
          type="trend"
          onMouseOver={this.mouseOver}
          onMouseOut={this.mouseOut}
        />
      </>
    );
  }
}
```

```javascript
class DailyChart extends React.Component {
  state = { hovering: false };
  mouseOver = () => this.setState({ hovering: true });
  mouseOut = () => this.setState({ hovering: false });
  render() {
    return (
      <>
        {this.state.hovering === true ? <Tooltip id={this.props.id} /> : null}
        <Chart
          type="daily"
          onMouseOver={this.mouseOver}
          onMouseOut={this.mouseOut}
        />
      </>
    );
  }
}
```

三个组件我们都写完了，你可能之前都是这么写 React 代码的。正如你看到的，非常不 `DRY` ,我们在三个组件中把同一套 hover 逻辑 重复了三次。

以上，问题就显而易见了。当一个新组件需要类似 hover 逻辑 时，我们应避免重复代码。那么，我们该如何解决呢？为了便于理解，先来了解一下编程当中的两个概念——回调 和 高阶函数。

在 JavaScript 当中，函数时第一公民。也就是说它可以像 objects/arrays/strings 被赋值给变量、被当作参数传递给函数和被函数返回。

```javascript
function add(x, y) {
  return x + y;
}

function addFive(x, addReference) {
  return addReference(x, 5);
}

addFive(10, add); // 5
```

如果你不了解的话，可能会感到有点儿绕。我们在 函数 addFive 中传入一个函数名为 addReference 的参数，并且在内部调用它。

以上，你把它当作参数传递的函数叫回调；接收函数作为参数的函数叫高阶函数。

为了更直观，我们把上述代码的命名概念化。

```javascript
function add(x, y) {
  return x + y;
}

function higherOrderFunction(x, callback) {
  return callback(x, 5);
}

higherOrderFunction(10, add);
```

这种模式很常见。如果你用过数组方法、jQuery 或 lodash 库，那么你就使用过 回调 和 高阶函数。

```javascript
[1, 2, 3].map(i => i + 5);

_.filter([1, 2, 3, 4], n => n % 2 === 0);

$("#btn").on("click", () => console.log("Callbacks are everywhere"));
```

回到之前写的那个例子。我们不仅需要 `addFive`，可能还需 `addTen` `addTwenty`等等。依照现在的写法，当我们写一个新函数的时候，不得不重复原有逻辑。

```javascript
function add(x, y) {
  return x + y;
}

function addFive(x, addReference) {
  return addReference(x, 5);
}

function addTen(x, addReference) {
  return addReference(x, 10);
}

function addTwenty(x, addReference) {
  return addReference(x, 20);
}

addFive(10, add); // 15
addTen(10, add); // 20
addTwenty(10, add); // 30
```

看起来还不错，但我们仍然重复了相同的逻辑。我们的目的是用更少的代码创建更多的 `adder函数`（addFive, addTen, addTwenty 等等）。鉴于此，我们创建一个`makeAdder函数` ，此函数接收一个数字和一个函数作为参数，长话少说，直接看代码。

```javascript
function add(x, y) {
  return x + y;
}

function makeAdder(x, addReference) {
  return function(y) {
    return addReference(x, y);
  };
}

const addFive = makeAdder(5, add);
const addTen = makeAdder(10, add);
const addTwenty = makeAdder(20, add);

addFive(10); // 15
addTen(10); // 20
addTwenty(10); // 30
```

完美，现在我们想要多少 `adder函数` 就能写多少，并且没必要写那么多重复代码。

> 如果你注意到，这种使用一个函数并将其应用一个或多个参数，但不是全部参数，在这个过程中创建并返回一个新函数叫『偏函数应用』。 JavaScript 当中的 `.bind`便是这种方法的一个例子。

但是，这些和我们最初写 React 代码重复又有什么关系呢？也像创建 `高阶函数makeAdder` 一样地创建类似 `高阶组件` 。看起来还不错，我们试试吧。

高阶函数

- 一个函数
- 接收一个回调函数为参数
- 返回一个新的函数
- 返回的函数可以调用传进去的回调函数

```javascript
function higherOrderFunction(callback) {
  return function() {
    return callback();
  };
}
```

高阶组件

- 一个组件
- 接收一个组件为参数
- 返回一个新的组件
- 返回的组件可以渲染当初传进去的组件

```javascript
function higherOrderComponent(Component) {
  return class extends React.Component {
    render() {
      return <Component />;
    }
  };
}
```

好，我们现在理解了高阶组件的基本概念。或许你还记得，最初面临的问题是在太多地方重复了 Hover 逻辑 部分。

```javascript
state = { hovering: false };
mouseOver = () => this.setState({ hovering: true });
mouseOut = () => this.setState({ hovering: false });
```

记住，我们希望高阶组件（命名为 `withHover`）能压缩 Hover 逻辑 部分，并带有 `hovering` 状态，这样能避免我们又重复 Hover 逻辑。

最终目标，无论何时我们想写一个带 Hover 状态的组件时，都可以把这个组件作为参数传入我们的高阶组件 `withHover`。

```javascript
const InfoWithHover = withHover(Info);
const TrendChartWithHover = withHover(TrendChart);
const DailyChartWithHover = withHover(DailyChart);
```

接着，无论什么组件传入 `withHover` ，都会返回组件本身，并且会接收一个 `hovering` 属性。

```javascript
function Info({ hovering, height }) {
  return (
    <>
      {hovering === true ? <Tooltip id={this.props.id} /> : null}
      <svg
        className="Icon-svg Icon--hoverable-svg"
        height={height}
        viewBox="0 0 16 16"
        width="16"
      >
        <path d="M9 8a1 1 0 0 0-1-1H5.5a1 1 0 1 0 0 2H7v4a1 1 0 0 0 2 0zM4 0h8a4 4 0 0 1 4 4v8a4 4 0 0 1-4 4H4a4 4 0 0 1-4-4V4a4 4 0 0 1 4-4zm4 5.5a1.5 1.5 0 1 0 0-3 1.5 1.5 0 0 0 0 3z" />
      </svg>
    </>
  );
}
```

现在，我们需要开始写 `withHover组件` 了。正如以上，需要做到以下三点：

- 接收一个『组件』为参数
- 返回一个新的组件
- 参数组件接收一个 “hovering” 属性

1、接收一个『组件』为参数

```javascript
function withHover(Component) {}
```

2、返回一个新的组件

```javascript
function withHover(Component) {
  return class WithHover extends React.Component {};
}
```

3、参数组件接收一个 “hovering” 属性
新问题来了，

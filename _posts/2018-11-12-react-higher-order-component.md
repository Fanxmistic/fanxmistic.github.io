---
layout: post
title: "深入浅出理解 React高阶组件"
date: 2018-11-12
categories: React
cover: "/assets/img/react-high.jpg"
tags: React JavaScript 前端
---

# React 高阶组件

> 开始之前，有两点需要说明一下：1、React 高阶组件 仅仅是一种模式，并不是 React 的基础知识；2、它不是开发 React app 的必要知识。你可以略过此文章，仍然可以开发 React app。然而，技多不压身，如果你也是一位 React 开发者，强烈建议你掌握它。

### 一、为什么需要高阶组件

如果你不知道 `Don't Repeat Yourself` 或 `D.R.Y`，那么在软件开发中必定走不太远。对于大多数开发者来说，它是一个开发准则。在这篇文章当中，我们将了解到如何在 React 当中运用 `DRY` 原则 —— `高阶组件`。开始阐述之前，我们先来认识一下问题所在。

假设我们要开发类似下图的功能。正如大多的项目一样，我们先按流程开发着。当开发到差不多的时候，你会发现页面上有很多，鼠标悬浮在某个元素上出现 `tooltip` 的场景。

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

三个组件我们都开发完成。但正如你看到的，非常不 `DRY` ,因为我们在三个组件中把同一套 hover 逻辑 重复了三次。

问题就显而易见了。当一个新组件需要类似 hover 逻辑 时，我们应避免重复。那么，我们该如何解决呢？为了便于理解，先来了解一下编程当中的两个概念—— **回调** 和 **高阶函数**。

### 二、什么是回调和高阶函数

在 JavaScript 当中，函数是第一公民。也就是说它可以像 objects/arrays/strings 被赋值给变量、被当作参数传递给函数和被函数返回。

```javascript
function add(x, y) {
  return x + y;
}

function addFive(x, addReference) {
  return addReference(x, 5);
}

addFive(10, add); // 5
```

你可能会感到有点儿绕：我们在 函数 addFive 中传入一个函数名为 addReference 的参数，并且在内部返回时调用它。类似这种情况，你把它当作参数传递的函数叫 **回调**；接收函数作为参数的函数叫 **高阶函数**。

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

这种写法其实很常见。如果你用过数组方法、jQuery 或 lodash 库，那么你就使用过 回调 和 高阶函数。

```javascript
[1, 2, 3].map(i => i + 5);

_.filter([1, 2, 3, 4], n => n % 2 === 0);

$("#btn").on("click", () => console.log("Callbacks are everywhere"));
```

### 三、高阶函数的简单应用

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

看起来还不错，但仍然有点重复。我们的目的是用更少的代码创建更多的 `adder函数`（addFive, addTen, addTwenty 等等）。鉴于此，我们创建一个`makeAdder函数` ，此函数接收一个 数字 和 一个函数 作为参数，长话少说，直接看代码。

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

很好，现在我们想要多少 `adder函数` 就能写多少，并且没必要写那么多重复代码。

> 这种使用一个函数并将其应用一个或多个参数，但不是全部参数，在这个过程中创建并返回一个新函数叫『偏函数应用』。 JavaScript 当中的 `.bind`便是这种方法的一个例子。

### 四、高阶组件

那么，这些和我们最初写 React 代码重复又有什么关系呢？也像创建 `高阶函数makeAdder` 一样地创建类似 `高阶组件` 。看起来还不错，我们试试吧。

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

### 五、高阶组件的简单应用

好，我们现在理解了高阶组件的基本概念。你应该还记得，最初面临的问题是在太多地方重复了 Hover 逻辑 部分。

```javascript
state = { hovering: false };
mouseOver = () => this.setState({ hovering: true });
mouseOut = () => this.setState({ hovering: false });
```

记住，我们希望高阶组件（命名为 `withHover`）能压缩 Hover 逻辑 部分，并带有 `hovering` 状态，这样能避免我们重复 Hover 逻辑。

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

新问题来了， `hovering` 该从哪里来？我们可以创建一个新的组件，把 `hovering` 当作该组件的状态，然后传给最初的那个参数组件。

```javascript
function withHover(Component) {
  return class WithHover extends React.Component {
    state = { hovering: false };
    mouseOver = () => this.setState({ hovering: true });
    mouseOut = () => this.setState({ hovering: false });
    render() {
      return (
        <div onMouseOver={this.mouseOver} onMouseOut={this.mouseOut}>
          <Component hovering={this.state.hovering} />
        </div>
      );
    }
  };
}
```

我想起了一句话：组件是把 props 转换成 UI 的过程；高阶组件是把一个组件转换成另一个组件的过程。

我们已经学习完了高阶函数的基础知识，但仍然有几点值得讨论。

### 六、高阶组件的进阶应用

回头看看组件 `withHover` ，还是有一点不足：就是它假想了用户传进去的参数组件必须要接收一个名为 hovering 的 prop;如果参数组件本身就有一个名为 hovering 的 prop，并且这个 prop 并不是来处理 hover 的， 就会造成命名冲突。我们可以尝试一下让用户自定义控制 hover 的 prop 命名。

```javascript
function withHover(Component, propName = "hovering") {
  return class WithHover extends React.Component {
    state = { hovering: false };
    mouseOver = () => this.setState({ hovering: true });
    mouseOut = () => this.setState({ hovering: false });
    render() {
      const props = {
        [propName]: this.state.hovering
      };

      return (
        <div onMouseOver={this.mouseOver} onMouseOut={this.mouseOut}>
          <Component {...props} />
        </div>
      );
    }
  };
}
```

在 withHover 中，我们给 propName 设定了一个默认值 `hovering`，用户也可以在组件中传入第二个参数自定义命名。

```javascript
function withHover(Component, propName = "hovering") {
  return class WithHover extends React.Component {
    state = { hovering: false };
    mouseOver = () => this.setState({ hovering: true });
    mouseOut = () => this.setState({ hovering: false });
    render() {
      const props = {
        [propName]: this.state.hovering
      };

      return (
        <div onMouseOver={this.mouseOver} onMouseOut={this.mouseOut}>
          <Component {...props} />
        </div>
      );
    }
  };
}

function Info({ showTooltip, height }) {
  return (
    <>
      {showTooltip === true ? <Tooltip id={this.props.id} /> : null}
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

const InfoWithHover = withHover(Info, "showTooltip");
```

你可能又注意到了另外一个问题，在组件 `Info` 中，它还接收一个名为 height 的 prop。按照现在这种写法，height 只能是 undefined，但我们期望能达到如下效果：

```javascript
const InfoWithHover = withHover(Info)

...

return <InfoWithHover height="16px" />
```

我们把 height 传入 `InfoWithHover` ，但是该如何使它生效呢？

```javascript
function withHover(Component, propName = "hovering") {
  return class WithHover extends React.Component {
    state = { hovering: false };
    mouseOver = () => this.setState({ hovering: true });
    mouseOut = () => this.setState({ hovering: false });
    render() {
      console.log(this.props); // { height: "16px" }

      const props = {
        [propName]: this.state.hovering
      };

      return (
        <div onMouseOver={this.mouseOver} onMouseOut={this.mouseOut}>
          <Component {...props} />
        </div>
      );
    }
  };
}
```

从 console 中可以看出， this.props 的值是 `{ height: "16px" }` 。我们要做的就是不管 this.props 为何值，都把 它传给参数组件 `Component`。

```javascript
    render() {
      const props = {
        [propName]: this.state.hovering,
        ...this.props,
      }

      return (
        <div onMouseOver={this.mouseOver} onMouseOut={this.mouseOut}>
          <Component {...props} />
        </div>
      );
    }
```

最终，我们可以看出，通过使用高阶组件可以有效地复用同套逻辑，避免过多的重复代码。但是，它真的没有任何缺点吗？显然不是。

### 七、高阶组件的小瑕疵

当我们使用高阶组件的时候，可能会发生 `inversion of control(控制反转)` 。想象一下，假如我们正使用 React Router 的 `withRouter` ，根据文档：无论是什么组件，它都会把 `match`, `location`和`history` 传给该组件的 prop。

```javascript
class Game extends React.Component {
  render() {
    const { match, location, history } = this.props // From React Router

    ...
  }
}

export default withRouter(Game)
```

从上可以看出，如果我们的组件 `Game` 也有命名为 `match`, `location`和`history` 的 prop 时，便会引发命名冲突。这个问题，我们在写组件 `withHover` 遇到过，并通过传入第二参数自定义命名的方式解决了该问题。但是当我们用到第三方库中的高阶组件时，就不一定会有那么幸运了。我们不得不修改我们自身组件 prop 的命名 或 停止使用第三方库中的该高阶组件。

### 八、结尾

本文是翻译自 [React Higher-Order Components]([React Higher-Order Components](https://tylermcginnis.com/react-higher-order-components/))，仅供学习参考。如果给您学习理解造成了迷惑，欢迎联系我。

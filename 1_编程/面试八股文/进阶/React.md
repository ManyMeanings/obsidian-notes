## Hooks

### useState

`useState` 主要用于给组件添加状态变量。注意，我们只能在**组件的顶层或自定义的 Hooks 中**调用 `useState`。

```tsx
const [age, setAge] = useState(42);
```

#### 懒初始化

对于需要计算得到的初始状态，可以使用函数传递给 `useState`。这样，函数只在**初次渲染**时执行，而非每次渲染。

```tsx
// 注意：传递函数本身，非执行结果
const [todos, setTodos] = useState(createInitialTodos);
```

#### 更新状态

大部分情况，直接更新状态即可；但**当新状态依赖于前一个状态时，推荐使用函数式更新**。这确保了更新准确性，特别是在并发模式下。

```tsx
// 正常更新
setAge(newState);

// 函数式更新
setState((prevState) => prevState + 1);

// 对象和数组更新需要创建新的引用
setForm({ ...form, name: e.target.value });

// 将函数作为状态更新
const [fn, setFn] = useState(() => someFunction);
function handleClick() {
  setFn(() => someOtherFunction);
}
```

### useEffect

在实际应用中，我们通常需要在组件中执行有副作用的操作，如数据获取、手动更改 DOM、设置订阅等。`useEffect` 就是为此设计的。

`useEffect` 接受两个参数：

1. **setup 函数**：这是包含我们的 Effect 逻辑的函数。setup 函数里还可以选择返回一个 `cleanup` 函数，`cleanup` 函数会在组件卸载的时候执行。
2. **依赖数组（optional dependencies）** ：这是 setup 代码内部引用的所有响应式值的列表。响应式值包括 props、state 以及直接在组件主体中声明的所有变量和函数。依赖数组可以不传递、传空数组和非空数组。

```tsx
useEffect(setup, dependencies?)
```

#### 执行时机

- 无依赖数组：每次渲染时都执行，包括初次渲染和每次更新。
- 依赖数组为空数组：只会在组件挂载时执行一次
- 有依赖项：每次这些依赖项发生变化时执行（_根据引用地址进行比较_），以及在组件挂载时执行一次。

#### 清理函数

清理函数不仅会在组件卸载时执行一次，每次依赖更新导致副作用函数重新执行之前，也会执行一次，用来清理上一次渲染的副作用。

### useLayoutEffect

`useLayoutEffect` 与 useEffect 的用法一模一样。

#### 与 useEffect 的区别

- `useEffect`: 执行时机是在 React 的渲染和提交阶段之后。这意味着当任何相关 DOM 更改被应用并且组件已被重新渲染后，`useEffect` 里的代码会执行。但它是**异步的**，所以可能会在浏览器的下一个绘制周期之后才执行。
- `useLayoutEffect`: 执行时机是在 React 的提交阶段之后，但在浏览器实际绘制屏幕之前。这使得你可以**同步地**读取或更改 DOM，然后让浏览器在下一次绘制时立即体现这些更改，从而避免不必要的闪烁或布局跳动。

#### 何时使用 `useEffect`

- **副作用与 DOM 无关**：例如，数据获取、设置订阅、手动更改浏览器的 URL 等。
- **不需要立即同步读取或更改 DOM**：如果你不关心可能的微小布局跳动或闪烁，那么 `useEffect` 就足够了。
- **性能考虑**：`useEffect` 通常对性能影响较小，因为它不会阻塞浏览器渲染。

#### 何时使用 `useLayoutEffect`

- **需要同步读取或更改 DOM**：例如，你需要读取元素的大小或位置并在渲染前进行调整。
- **防止闪烁**：在某些情况下，异步的 `useEffect` 可能会导致可见的布局跳动或闪烁。例如，动画的启动或某些可见的快速 DOM 更改。
- **模拟生命周期方法**：如果你正在将旧的类组件迁移到功能组件，并需要模拟 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 的同步行为。

#### 注意事项

- 避免过度使用 `useLayoutEffect`，因为它是同步的，可能会影响应用的性能。只有当你确实需要同步的 DOM 操作时才使用它。
- 如果代码在服务器端渲染（SSR）中出现问题，考虑回退到 `useEffect`。`useLayoutEffect` 在服务器端渲染时不会运行，可能会引发警告或错误。

### useReducer

`useReducer` 允许我们使用 action 和 reducer 的方式来组织复杂的状态逻辑，使其变得更加清晰和模块化。

`useReducer`**接收三个参数**：

- reducer 函数：指定如何更新状态的还原函数，它必须是**纯函数**，以 state 和 dispatch 的参数为参数，并返回下一个状态。
- 初始状态：初始状态的计算值。
- （可选的）初始化参数：用于返回初始状态。如果未指定，初始状态将设置为 initialArg；如果有指定，初始状态将被设置为调用 `init(initialArg)` 的结果。

`useReducer`**返回两个参数**：

- 当前的状态：当前状态。在第一次渲染时，它会被设置为 `init(initialArg)` 或 initialArg（如果没有 init 的情况下）。
- dispatch：调度函数，用于调用 reducer 函数，以更新状态并触发重新渲染。

基本形式如下：

```tsx
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```

#### `dispatch` 更新机制

注意：`dispatch` 调用后，状态更新是**异步**的，因此立刻读取状态可能仍是旧的。

```tsx
function addCount() {
  dispatch({ type: "increment" });
  console.log(state.count); // 打印出来的不是新值
}

<button onClick={addCount}>+</button>;
```

React 对 `dispatch` 有一个优化机制：如果 `dispatch` 触发更新前后的值相等（使用 [Object.is](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FObject%2Fis "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is") 判断），实际上 React 不会进行重新渲染，这是出于性能考虑。

### useContext

`useContext` 可以**让开发者通过 `context` 实现跨层级共享状态**。

```tsx
// 1. 创建 context
const MyContext = React.createContext(defaultValue);

// 2. 使用 Provider
<MyContext.Provider value={someValue}>{/* 子组件 */}</MyContext.Provider>;

// 3. 在子组件中获取
function MyComponent() {
  const contextValue = useContext(MyContext);
  return <div>{contextValue}</div>;
}
```

#### 动态 Context 值

Context 的值可以是 `useState` 返回的状态和状态更新函数；也可以与 `useReducer` 结合起来使用，以便从组件中提取与某些状态相关的逻辑。

#### 性能优化

当 `Provider` 的 `value` 属性值发生变化时，所有使用了 `useContext` 的组件都将重新渲染。如果 `value` 经常变化，或者消费者组件很多，那么这会引起大量的不必要的渲染。

- 粒度化 Context：分解成小的 context，按需使用
- 使用 `useMemo` 和 `useCallback` 对 `value` 中的参数进行缓存

### useRef

`useRef` 返回一个可变的 ref 对象，通过 `.current` 可以获取保存在 `useRef` 的值。

```tsx
// 定义
const inputRef = useRef(null);

// 使用
console.log(inputRef.current);
```

#### 与 `useState` 的区别

1. **持久性**：`useRef` 的返回对象在组件的整个生命周期中都是持久的，而不是每次渲染都重新创建。（_类似于闭包的概念_）
2. **不会触发渲染**：当 `useState` 中的状态改变时，组件会重新渲染。而当 `useRef` 的 `.current` 属性改变时，组件不会重新渲染。

总结来说，`useRef` 既能保存状态，还不会在更新时触发渲染。

#### 常见用法

- 访问 DOM 元素
- 保存状态但不触发渲染
- 保存上一次的 props 或 state

#### 注意事项

避免在组件渲染期间读/写 `ref.current`，原因：

- **不稳定的 UI**：由于 React 在多次渲染中可能使用异步和优化技术，直接在渲染期间修改 refs 可能导致 UI 不一致。
- **依赖更新**：如果其他效应或钩子依赖于 ref 的值，它们可能不会在期望的时刻运行，因为直接修改 ref 不会触发重新渲染或其他效应。

这是为什么我们通常在 `useEffect` 内部更新 refs。在 `useEffect` 内部，我们可以确保组件已经完成渲染，并且不会在渲染期间发生任何不期望的副作用。

### useMemo

`useMemo` 通过缓存计算结果，避免在组件渲染时进行不必要的重复计算，从而优化性能。这意味着只有当其依赖项发生变化时，`useMemo` 才会重新计算这个值，否则它将重用之前的结果。

它接收两个参数：

- `calculateValue`：这是一个用于计算我们想要缓存的值的函数。为了确保结果的稳定性和预测性，这个函数应该是一个纯函数。这意味着，它在相同的输入下总是返回相同的输出，并且没有任何副作用。
- `dependencies`：这是一个数组，包含 `useMemo` 所依赖的变量或值。当数组中的任何值发生变化时，`calculateValue` 函数将被重新执行。

```tsx
const cachedValue = useMemo(calculateValue, dependencies);
```

#### 使用场景

- **缓存需要大量计算的数据**
- **减少重复渲染开销**：确保对象或数组的引用在数据不变时保持不变，从而避免子组件的不必要渲染。
- **缓存组件**：
  - 当你想避免因为**数据变化**而产生的不必要的**计算**时，使用 `useMemo`
  - 当你想避免因为**props 未变**而产生的不必要的**组件重渲染**时，使用高阶组件 `memo`

#### 避免过度优化

使用 `useMemo` 的关键是权衡。其目的是避免不必要的计算，但也要注意不要滥用，因为维持这些缓存值也是有开销的。最佳的做法是先写出清晰和可读的代码，然后在性能瓶颈出现时，再考虑优化。

### useCallback

`useCallback` 是对 `useMemo` 的特化，它可以返回一个缓存版本的函数，只有当它的依赖项改变时，函数才会被重新创建。这意味着如果依赖没有改变，函数引用保持不变，从而避免了因函数引用改变导致的不必要的重新渲染。

```tsx
const memoizedCallback = useCallback(
  () => {
    // 函数体
  },
  [dependency1, dependency2, ...] // 依赖数组
);

```

#### 使用场景

1. **子组件的性能优化**：当你将函数作为 prop 传递给已经通过 `React.memo` 进行优化的子组件时，使用 `useCallback` 可以确保子组件不会因为父组件中的函数重建而进行不必要的重新渲染。
2. **Hook 依赖**：如果你正在传递的函数会被用作其他 Hook（例如 `useEffect`）的依赖时，使用 `useCallback` 可确保函数的稳定性，从而避免不必要的副作用的执行。
3. **复杂计算与频繁的重新渲染**：在应用涉及很多细粒度的交互，如绘图应用或其它需要大量操作和反馈的场景，使用 `useCallback` 可以避免因频繁的渲染而导致的性能问题。

#### 与 useMemo 的区别

**用途与缓存的内容不同：**

- `useMemo`: 用于缓存复杂函数的计算结果或者构造的值。**它返回缓存的结果**。
- `useCallback`: 用于**缓存函数本身**，确保函数的引用在依赖没有改变时保持稳定。

从本质上说，`useCallback(fn, deps)` 就是 `useMemo(() => fn, deps)` 的语法糖。

### useTransition

在 React 的并发模式下，`useTransition` 允许我们中断或延后某些状态更新，以便于能够在长时间的计算或数据拉取时保持 UI 的响应性。

它返回两个值：

- **`isPending`**：是一个布尔值，当过渡状态仍在进行中时，其值为 `true`；否则为 `false`。
- **`startTransition`**：是一个函数，当你希望启动一个新的过渡状态时调用它。

```tsx
const [isPending, startTransition] = useTransition();
```

#### 背景

React 的默认渲染行为是同步的，React 总是在一个操作或者状态更新后再执行下一个操作或状态更新。在大部分场景下，这种即时响应是理想的，因为它确保了 UI 与内部状态保持紧密同步。但在密集或资源繁重的更新场景下——如加载大量数据时，这种同步行为可能导致整个应用的 UI 卡顿甚至没有响应。我们把这种 UI 卡顿现象叫做“阻塞”。

React v18 开始，官方引入了并发模式（Concurrent Mode）和一些相关的 Hooks，其中之一就是 `useTransition`。开发者可以将某些状态更新标记为“可中断”的，从而允许 React 在必要时打断这些更新，先处理其他更为紧急的任务。我们把这种渲染效果称作“非阻塞 UI”。

#### 原理

- **并发模式下的状态更新分类**: 在 React 的并发模式中，不是所有的状态更新都被视为等同的。React 将更新分为不同的优先级类别，其中某些更新（如输入处理）被认为是更加紧急的，而其他更新（如从服务器获取数据）则可以中断或者延后更新。
- **使用 `startTransition` 进行状态更新**: 当你使用 `startTransition` 函数进行状态更新时，你实际上告诉 React：这个更新不是非常紧急的，如果有更重要的更新要处理，你可以中断或延后这个次要更新。
- **`isPending` 的用途**: `isPending` 为我们提供了一个标识，告诉我们是否有一个 `startTransition` 正在执行。我们可以根据 `isPending` 来设置过渡状态的样式。

#### 使用场景

想象一个场景，页面上有多个 tab，其中一个请求耗时较长，我们快速点击 tab 切换内容，但总是会在请求耗时的 tab 上卡顿一下。

如果我们想用 `useTransition` 保持 UI 的响应，只需要用 `startTransition` 包裹切换选项卡的 `set`：

```tsx
const [isPending, startTransition] = React.useTransition();

function selectTab(nextTab) {
  startTransition(() => {
    setTab(nextTab);
  });
}
```

这样我们快速切换 tab，无论点到哪一个 tab 都不会卡顿。

当与路由结合使用时，`React.Suspense` 允许我们延迟渲染新的路由内容，直到所需的数据被完全加载。而 `useTransition` 则允许我们可控地开始这种可能导致 UI 变化的过渡——导航到新页面。

```tsx
function handleNavigation(newLocation) {
  startTransition(() => {
    setLocation(newLocation);
  });
}
// 使用 isPending 显示或隐藏全局加载指示器
{
  isPending && <LoadingIndicator />;
}
```

### useDeferredValue

`useDeferredValue` 的作用是：延迟某个值的更新，使高优先级的任务可以先行完成。

```jsx
const deferredValue = useDeferredValue(someValue);
```

其中 `someValue` 是你想要延迟的值，它可以是任何类型。

`deferredValue` 的渲染有两种情况：

1. **在初始渲染时**，`deferredValue` 的值将与 `someValue` 的值相同。
2. **在 UI 更新期间**，因为 `deferredValue` 的优先级较低，即使并发模式下 `deferredValue` 已在后台更新，React 也会先使用旧值渲染，当其它高优先级的状态更新完成，才会把 `deferredValue` 新值渲染出来。

#### 与 useTransition 的区别

- `useTransition` 主要关注点是**状态的过渡**。它允许开发者控制某个更新的延迟更新，还提供了过渡标识，让开发者能够添加过渡反馈。
- `useDeferredValue` 主要关注点是**单个值的延迟更新**。它允许你把特定状态的更新标记为低优先级。

简而言之，如果你想提供过渡反馈，就用 `useTransition`，如果不需要提供过渡反馈，用 `useDeferredValue` 就可以。

#### 使用场景

想象一个场景，你在大数据量场景下做查询，前端渲染就需要延迟更新列表，还希望只有最后一次查询的数据被保留，这时候 `useDeferredValue` 就派上用场了，例如：

```tsx
export default function App() {
  const [text, setText] = useState("");
  const deferredText = useDeferredValue(text);
  return (
    <>
      {" "}
      {/* 输入框的值直接与 text 绑定，所以输入框会实时显示用户的输入 */} <input
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      {/* SlowList 组件接受 deferredText 作为属性，渲染优先级会被降低 */}
      {/* 在 UI 真正更新前，如果 deferredText 被更新多次，也只会保留最后一次的结果 */}
      <SlowList text={deferredText} />
    </>
  );
}
```

### useImperativeHandle

Refs 默认只能访问 DOM 节点或组件实例。这时，如何自定义我们想要暴露给父组件的实例方法或属性呢？答案就是使用 `useImperativeHandle`。

`useImperativeHandle` 通常与 `forwardRef` 一起使用，以便将 ref 传递给函数组件：

```tsx
const ForwardedCustomInput = forwardRef(function CustomInput(props, ref) {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    clear: () => {
      inputRef.current.value = "";
    },
  }));

  return <input ref={inputRef} />;
});
```

现在，当你使用 `ForwardedCustomInput` 并为其提供一个 ref 时，你可以直接调用 `clear` 方法

```tsx
function App() {
  const inputRef = useRef();

  return (
    <div>
      <ForwardedCustomInput ref={inputRef} />
      <button onClick={() => inputRef.current.clear()}>Clear Input</button>
    </div>
  );
}
```

# 你可能并不需要 useEffect

>   原文地址：[You Might Not Need an Effect](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/you-might-not-need-an-effect)

Effects 是 React 范式的一个 **Escape Hatch**。它们让你“走出” React 并将你的组件与一些外部系统同步，例如非 React 小部件、网络或浏览器 DOM。如果不涉及外部系统（例如，如果你想在某些道具或状态更改时更新组件的状态），则不需要 Effect。删除不必要的 Effect 将使你的代码更易于遵循、运行更快且不易出错。

## 如何删除不必要的效果

有两种不需要效果的常见情况：

-   **不需要 Effects 来转换数据以进行渲染。**例如，假设想在显示列表之前对其进行过滤。你可能想编写一个在列表更改时更新状态变量的 Effect。然而，这是低效的。当你更新组件的状态时，React 将首先调用你的组件函数来计算应该在屏幕上显示的内容。然后 React 会将这些更改  [“commit”](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/render-and-commit)  到 DOM，然后更新屏幕。然后 React 会运行你的 Effects。如果你的 Effect 也立即更新状态，这将从头开始重新启动整个过程！为避免不必要的渲染通道，请在组件的顶层转换所有数据。每当你的 props 或 state 发生变化时，该代码将自动重新运行。
-   **不需要 Effects 来处理用户事件。**例如，假设您要发送 `/api/buy POST` 请求并在用户购买产品时显示通知。在购买按钮单击事件处理程序中，您确切地知道发生了什么。当 Effect 运行时，您不知道用户做了什么（例如，单击了哪个按钮）。这就是为什么您通常会在相应的事件处理程序中处理用户事件。

你确实需要 Effects 与外部系统[同步](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events)。例如，你可以编写一个使 jQuery 小部件与 React 状态保持同步的效果。你还可以使用 Effects 获取数据：例如，你可以将搜索结果与当前搜索查询同步。请记住，现代[框架](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/start-a-new-react-project#building-with-a-full-featured-framework)提供了比直接在组件中编写效果更有效的内置数据获取机制。

为了帮助你获得正确的直觉，让我们看一些常见的具体示例！

### 根据 props 或 state 更新 state

假设您有一个具有两个状态变量的组件：`firstName` 和 `lastName`。您想通过连接它们来计算 `fullName`。此外，您希望 `fullName` 在 `firstName` 或 `lastName` 更改时更新。你的第一反应可能是添加一个 `fullName` 状态变量并在 Effect 中更新它：

```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');


  // 🔴 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

这种写法变得相当的复杂，并且效率也很低下：使用 `fullName` 的旧值执行整个渲染传递，然后立即使用更新的值重新渲染。删除 `fullName` 的 state 和 effect：

```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

**当一些东西可以从现有的 props 或 state 中计算出来时，[不要把它放在 state 中。](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/choosing-the-state-structure#avoid-redundant-state)相反，在渲染期间计算它。**这使你的代码更快（你避免了额外的“级联”更新），更简单（你删除了一些代码），并且更不容易出错（你避免了由不同状态变量彼此不同步引起的错误）。如果你觉得这种方法很新，[Thinking in React](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/thinking-in-react#step-3-find-the-minimal-but-complete-representation-of-ui-state)有一些关于应该进入状态的指导。

### 缓存复杂计算结果

该组件通过获取 `todos` 数据 并根据 `filter` 属性过滤它们来计算 `visibleTodos`。你可能会想将结果存储在状态变量中，并在 Effect 中更新它：

```jsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');


  // 🔴 Avoid: redundant state and unnecessary Effect
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);

  // ...
}
```

就像前面的例子一样，这既不必要又低效。首先，移除状态和效果：

```jsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ This is fine if getFilteredTodos() is not slow.
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}
```

在很多情况下，这段代码很好！但 `getFilteredTodos()` 运行速度也许很慢或者 `todos` 数据过于庞大。 在这种情况下，你不想重新运行 `getFilteredTodos()` 来计算某些不相关的状态变量如 `newTodo` 已经发生变化。

你可以通过将其封装在 [`useMemo`](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/apis/usememo) 中来缓存（或 [“memozie”](https://en.wikipedia.org/wiki/Memoization)）复杂计算：

```jsx
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Does not re-run unless todos or filter change
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```

或者，写成一行：

```jsx
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ Does not re-run getFilteredTodos() unless todos or filter change
  const visibleTodos = useMemo(() => getFilteredTodos(todos, filter), [todos, filter]);
  // ...
}
```

**这告诉 React，你不希望内部函数重新运行，除非 `todos` 或 `filter` 已更改。**在初始渲染期间，React 会记住 `getFilteredTodos()` 的返回值。在下一次渲染期间，它将检查`todos` 或 `filter`是否发生变化。如果发生变化，`useMemo` 将返回它存储的最后一个结果。但如果没有变化，React 将再次调用封装的函数（并存储该结果）。

通过 [`useMemo`](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/apis/usememo) 封装的函数只会在渲染期间运行，因此这个方式只适用于[纯计算](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/keeping-components-pure)。

### 当 props 变化时重置所有状态

`ProfilePage` 组件接收了一个 `userId` 属性。该页面包含注释输入，并且使用 `comment` 状态变量来保存其值。有一天，你注意到一个问题：当你从一个配置文件导航到另一个配置文件时，`comment` 状态不会重置。因此，很容易意外地在错误的用户的个人资料上发表评论。要解决此问题，每当 `userId` 发生更改时，都需要清除 `comment` 状态变量：

```jsx
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🔴 Avoid: Resetting state on prop change in an Effect
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ...
}
```

这是低效的，因为 `ProfilePage` 及其子级将首先使用旧值进行渲染，然后再次渲染。这样也是很复杂的，因为你需要在 `ProfilePage` 组件中的每个拥有某种状态的子组件里面执行此操作。例如，如果注释 UI 是嵌套的，则你也希望清除嵌套的注释状态。

相反，你可以通过给它一个显式的 key 来告诉 React 每个用户的配置文件在概念上是*不同的配置文件。*将你的组件一分为二，并通过 `key` 属性从外部组件传递到内部组件：

```jsx
export default function ProfilePage({ userId }) {
  return (
    <Profile userId={userId} key={userId} />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [comment, setComment] = useState('');
  // ...
}
```

通常，当同一组件在同一位置渲染时，React 会保留状态。**通过将 `userId` 作为 `key` 传递给 `Profile` 组件，你要求 React 将两个具有不同 `userId` 的 `Profile` 组件视为两个不应共享任何状态的不同组件。**每当 key（已设置为 `userId` ）发生更改时，React 都会重新创建 DOM 并重置 `Profile` 组件及其所有子组件的状态。因此，在配置文件之间导航时，`comment` 字段将自动清除。

请注意，在此示例中，仅导出外部的 `ProfilePage` 组件，并且对项目中的其他文件可见。渲染 `ProfilePage` 组件时不需要将 key 传递给它：它们将 `userId` 作为常规属性传递。实际上的实现细节是 `ProfilePage` 将 `userId` 作为内部组件 `Profile` 的 key 传递。

### 当 prop 变化时调整部分 state

有时候，你只希望在 prop 发生变化时重置或调整部分的 state，而不是全部。

 `List` 组件接收一个集合类型的变量 `items` 作为属性，并在 `selection` 状态变量中维护所选项目。每当属性 `items` 收到不同的数组时，你想把将 `selection` 重置为 `null`：

```jsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // 🔴 Avoid: Adjusting state on prop change in an Effect
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
}
```

这并不是一个理想的方式。每次 `items` 发生变化时，`List` 组件及其所有子组件都会先根据旧值去渲染。然后 React 将更新 DOM 并运行 Effects。最后，调用 `setSelection(null)` 将会导致再次重新渲染 `List` 组件及其所有子组件，再次重新启动整个过程。

首先删除 Effect。相反，在渲染过程中直接调整状态：

```jsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // Better: Adjust the state while rendering
  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
  // ...
}
```

[像这样存储来自先前渲染的信息](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/apis/usestate#storing-information-from-previous-renders)可能很难理解，但它比在 Effect 中更新相同的状态要好。在上面的代码中，`setSelection` 会在渲染期间直接调用。React 将在执行 `return` 后**立即**重新渲染 `List`，这时 React 还没有渲染 `List` 子节点或更新 DOM，所以这可以让 `List` 子节点跳过渲染 `selection` 的旧值。

当你在渲染期间更新组件时，React 会丢弃返回的 JSX 并立即重试渲染。为了避免非常缓慢的级联重试，React 只允许你在渲染期间更新相同组件的状态。如果你在渲染期间更新另一个组件的状态，则会看到一个错误。为了避免循环，需要设置一个判断条件：`items != prevItems`。你可以像这样调整状态，但任何其他副作用（如更改 DOM 或设置超时）应保留在事件处理程序或效果中，以[保持你的组件可预测](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/keeping-components-pure)。

**尽管这种模式比 Effect 更有效，但大多数组件也不应该需要它。**不管你怎么做，根据 props 或其他状态调整状态会使你的数据流变得更难理解和调试。始终检查是否可以[使用 key 重置所有状态](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/you-might-not-need-an-effect#resetting-all-state-when-a-prop-changes)或[在渲染期间计算所有内容](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/you-might-not-need-an-effect#updating-state-based-on-props-or-state)。例如，你可以存储所选 **ID**，而不是存储（和重置）选定 **item**：

```jsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Best: Calculate everything during rendering
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```

现在根本不需要“调整”状态。如果具有所选 ID 的项目在列表中，则它保持选中状态。如果不是，`selection` 则在渲染期间计算为 `null`，因为未找到匹配项。此行为略有不同，但可以说它更好，因为对 `items` 的大多数更改现在都会保留所选内容。但是，您需要在下面的所有逻辑中使用 `selection`，因为具有 `selectedId` 的项目可能不存在。

### 在事件处理程序之间共享逻辑

假设您有一个产品页面，其中包含两个按钮（“购买”和“结帐”），这两个按钮都可以让您购买该产品。每当用户将产品放入购物车时，你都希望显示通知 Toast。将 `showToast()` 调用添加到两个按钮的单击处理程序中感觉重复，因此您可能会想将此逻辑放在 Effect 中：

```jsx
function ProductPage({ product, addToCart }) {
  // 🔴 Avoid: Event-specific logic inside an Effect
  useEffect(() => {
    if (product.isInCart) {
      showToast(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);
  function handleBuyClick() {
    addToCart(product);
  }
  function handleCheckoutClick() {
    addToCart(product);
    navigateTo('/checkout');
  }
  // ...
}
```

这个 Effect 是不必要的。它也极有可能导致错误。例如，假设你的应用在页面重新加载之间“记住”购物车。如果您将产品添加到购物车一次并刷新页面，则通知 Toast 将再次出现。每次刷新该产品的页面时，它都会继续出现。这是因为 `product.isInCart` 在页面加载时已经为 `true`，因此上面的 Effect 将调用 `showToast()`。

**当你不确定某些代码应该在 Effect 中还是在事件处理程序中时，问问自己为什么需要运行此代码。仅将效果用于应该运行的代码，因为该组件已显示给用户。**在这个例子中，Toast 应该出现是因为用户*按下了按钮*，而不是因为显示了产品页面！删除 Effect 并将共享逻辑放入你从两个事件处理程序调用的函数中：

```jsx
function ProductPage({ product, addToCart }) {
  // ✅ Good: Event-specific logic is called from event handlers
  function buyProduct() {
    addToCart(product);
    showToast(`Added ${product.name} to the shopping cart!`);    
  }
  function handleBuyClick() {
    buyProduct();
  }
  function handleCheckoutClick() {
    buyProduct();
    navigateTo('/checkout');
  }
  // ...
}
```

这既消除了不必要的 Effect，又修复了错误。

### 发送 POST 请求

这个 `Form` 组件发送两种 POST 请求。它在挂载时发送分析事件。当你填写表单并单击提交按钮时，它将向中断发送一个 POST 请求：`/api/register`

```jsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  // ✅ Good: This logic should run because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);
    
  // 🔴 Avoid: Event-specific logic inside an Effect
  const [jsonToSubmit, setJsonToSubmit] = useState(null);
  useEffect(() => {
    if (jsonToSubmit !== null) {
      post('/api/register', jsonToSubmit);
    }
  }, [jsonToSubmit]);
    
  function handleSubmit(e) {
    e.preventDefault();
    setJsonToSubmit({ firstName, lastName });
  }
  // ...
}
```

让我们应用与之前示例中相同的标准。

POST 请求 `/analytics/event` 应该保留在 Effect 中。因为发送 `/analytics/event` 需要在表单组件已经被显示之后触发。（它会在开发模式中触发两次，[参考此处](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/synchronizing-with-effects#sending-analytics)了解如何处理。）

但是，POST 请求 `/api/register` 不是由正在显示的表单引起的。你只想在一个特定时刻发送请求：当用户按下按钮时。它应该只发生**在那个特定的交互上**。删除第二个 Effect 并将该 POST 请求移动到事件处理程序中：

```jsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic runs because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  function handleSubmit(e) {
    e.preventDefault();
    // ✅ Good: Event-specific logic is in the event handler
    post('/api/register', { firstName, lastName });
  }
  // ...
}
```

当你选择是否将某些逻辑放入事件处理程序或 Effect 时，你需要扪心自问：从用户的角度来看它是**一种什么样的逻辑**。如果此逻辑是由特定交互引起的，请将其保存在事件处理程序中。如果它是由用户在屏幕上**看到**组件引起的，请将其保留在 Effect 中。

### 初始化应用程序

某些逻辑只应在应用加载时运行一次。你可以把它放在顶层组件的一个 Effect 中：

```jsx
function App() {
  // 🔴 Avoid: Effects with logic that should only ever run once
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```

但是，你很快会发现它[在开发模式中运行了两次](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)。这可能会导致问题——例如，它可能会使身份验证令牌无效，因为该函数并非设计为被调用两次。通常，你的组件应该能够适应重新安装。这包括你的顶级组件 `App`。尽管它可能永远不会在实际生产中重新安装，但在所有组件中遵循相同的约束可以更容易地移动和重用代码。如果某些逻辑必须在**每个应用程序加载时运行一次**，而不是**每个组件安装一次**，你可以添加一个顶级变量来跟踪它是否已经执行，并始终跳过重新运行它：

```jsx
let didInit = false;


function App() {
  useEffect(() => {
    if (!didInit) {
      didInit = true;
      // ✅ Only runs once per app load
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
  // ...
}
```

你还可以在模块初始化期间和应用程序呈现之前运行它：

```jsx
if (typeof window !== 'undefined') { // Check if we're running in the browser.
   // ✅ Only runs once per app load
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

当你的组件被导入时，顶层的代码会运行一次——即使它最终没有被渲染。为避免在导入任意组件时出现减速或意外行为，请不要过度使用此模式。将应用程序范围的初始化逻辑保留在根组件模块中如 `App.js`，或在应用程序的入口模块中。

### 通知父组件状态变化

假设您正在编写一个具有内部 `isOn` 状态的 `Toggle` 组件，该状态可以为 `true` 或 `false`。有几种不同的方法可以切换它（通过单击或拖动）。每当 `Toggle` 内部状态发生更改时，你都希望通知父组件，因此你公开了一个 onChange 事件并从 Effect 中调用它：

```jsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);


  // 🔴 Avoid: The onChange handler runs too late
  useEffect(() => {
    onChange(isOn);
  }, [isOn, onChange])

  function handleClick() {
    setIsOn(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      setIsOn(true);
    } else {
      setIsOn(false);
    }
  }
  // ...
}
```

和之前一样，这并不理想。首先 `Toggle` 更新了它的状态，然后 React 更新屏幕。接着 React 运行 Effect，它调用从父组件传递的 `onChange` 函数。现在父组件将更新自己的状态，开始另一个渲染过程。但是最好的办法是一次完成所有事情。

删除 Effect 并在同一个事件处理程序中更新**两个组件的状态**：

```jsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    // ✅ Good: Perform all updates during the event that caused them
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  function handleClick() {
    updateToggle(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      updateToggle(true);
    } else {
      updateToggle(false);
    }
  }
  // ...
}
```

使用这种方法，`Toggle` 组件及其父组件都会在事件期间更新它们的状态。React 将来自不同组件的[批量更新](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/queueing-a-series-of-state-updates)放在一起，因此只会进行一次渲染。

你也许还可以完全删除 `isOn` 状态，而是从父组件接收：

```jsx
// ✅ Also good: the component is fully controlled by its parent
function Toggle({ isOn, onChange }) {
  function handleClick() {
    onChange(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      onChange(true);
    } else {
      onChange(false);
    }
  }
  // ...
}
```

[状态提示](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/sharing-state-between-components)可以让父组件通过控制 `Toggle` 组件来完全控制自己的状态。这意味着父组件将必须包含更多逻辑，但总体上需要担心的状态会更少。每当你尝试使两个不同的状态变量保持同步时，这就表示你需要进行状态提升了。

### 将数据传递给父级

`Child` 组件获取一些数据，然后在 Effect 中将其传递 `Parent`组件：

```jsx
function Parent() {
  const [data, setData] = useState(null);
  // ...
  return <Child onFetched={setData} />;
}

function Child({ onFetched }) {
  const data = useSomeAPI();
  // 🔴 Avoid: Passing data to the parent in an Effect
  useEffect(() => {
    if (data) {
      onFetched(data);
    }
  }, [onFetched, data]);
  // ...
}
```

在 React 中，数据从父组件流向其子组件。当你在屏幕上看到错误的时候，你可以通过沿着组件链向上追踪信息的来源，直到找到哪个组件传递了错误的 prop 或具有错误的状态。当子组件在 Effects 中更新其父组件的状态时，数据流变得非常难以追踪。由于子组件和父组件都需要相同的数据，因此让父组件获取该数据，并将其传递给子组件：

```jsx
function Parent() {
  const data = useSomeAPI();
  // ...
  // ✅ Good: Passing data down to the child
  return <Child data={data} />;
}

function Child({ data }) {
  // ...
}
```

这更简单并保持数据流可预测：数据从父级向下流向子级。

### 订阅外部数据源

有时，你的组件可能需要订阅 React 状态之外的一些数据。此数据可能来自第三方库或内置浏览器 API。由于这些数据可能会在 React 不知情的情况下发生变化，因此你需要手动为组件订阅它。这通常通过 Effect 来完成，例如：

```jsx
function useOnlineStatus() {
  // Not ideal: Manual store subscription in an Effect
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function updateState() {
      setIsOnline(navigator.onLine);
    }
    updateState();
    window.addEventListener('online', updateState);
    window.addEventListener('offline', updateState);
    return () => {
      window.removeEventListener('online', updateState);
      window.removeEventListener('offline', updateState);
    };
  }, []);
  return isOnline;
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

在这里，组件订阅外部数据存储（在本例中为浏览器的 `navigator.onLine` API）。由于该 API 在服务器上不存在（因此不能用于生成初始 HTML），最初状态设置为 `ture`。 每当浏览器中该数据存储的值发生变化时，组件都会更新其状态。

尽管为此使用 Effects 很常见，但 React 有一个专门构建的 Hook，用于订阅首选的外部存储。删除 Effect 并将其替换为调用：[`useSyncExternalStore`](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/apis/usesyncexternalstore)

```jsx
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function useOnlineStatus() {
  // ✅ Good: Subscribing to an external store with a built-in Hook
  return useSyncExternalStore(
    subscribe, // React won't resubscribe for as long as you pass the same function
    () => navigator.onLine, // How to get the value on the client
    () => true // How to get the value on the server
  );
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

与使用 Effect 手动将可变数据同步到 React 状态相比，这种方法更不容易出错。通常，你将像上面这样编写一个`useOnlineStatus()`这样的自定义 Hook，这样你就不需要在各个组件中重复此代码。[阅读有关从 React 组件订阅外部存储的更多信息。](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/apis/usesyncexternalstore)

### 获取数据

许多应用程序使用 Effects 来启动数据获取。像这样编写 Effect 来获取数据是很常见的：

```jsx
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);

  useEffect(() => {
    // 🔴 Avoid: Fetching without cleanup logic
    fetchResults(query, page).then(json => {
      setResults(json);
    });
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

你**不需要**将获取数据的逻辑移动到一个事件处理程序。

这似乎与前面的示例相矛盾，在这些示例中，您需要将逻辑放入事件处理程序中！但是要考虑到输入事件并不是数据获取的主要出发原因。搜索输入通常是从 URL 预先填充的，用户可能会在不接触输入的情况下导航“后退”和“前进”。`page` 和 `query` 来自何处并不重要。虽然此组件可见，但你希望根据当前 `page` 和 `query` 使结果与网络中的数据保持同步。这就是为什么它要用一个 Effect。

但是，上面的代码有一个错误。想象一下，你快速输入 `hello` 字符串。然后，`qeury` 将从 `"h"` 更改为 `"he"`，`"hel"`，`"hell"`和 `"hello"`。这将触发5 次搜索请求，但是无法知道响应将会以何种顺序到达。例如，`"hell" `响应可能在 `"hello"` 响应之后到达。由于它会最后调用 ` setResults()`，因此将显示错误的搜索结果。这被称为 [“竞争条件(race condition)”](https://en.wikipedia.org/wiki/Race_condition)：两个不同的请求相互“竞争”，并且以不同于预期的顺序出现。

**要修复竞争条件，你需要[添加一个清理函数](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/learn/synchronizing-with-effects#fetching-data)来忽略过时的响应：**

```jsx
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1); 
  useEffect(() => {
    let ignore = false;
    fetchResults(query, page).then(json => {
      if (!ignore) {
        setResults(json);
      }
    });
    return () => {
      ignore = true;
    };
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

这确保了当你的 Effect 获取数据时，除了最后一个请求的响应之外的所有响应都将被忽略。

处理竞争条件并不是实现数据获取的唯一困难。你可能还想考虑如何缓存响应（以便用户可以单击返回并立即查看上一个屏幕而不是微调器），如何在服务器上获取它们（以便初始服务器呈现的 HTML 包含获取内容而不是微调器），以及如何避免网络瀑布（这样需要获取数据的子组件不必等待其上方的每个父组件完成获取数据才能开始）。**这些问题适用于任何 UI 库，而不仅仅是 React。解决它们并非易事，这就是现代[框架](https://beta-reactjs-org-git-tef-fbopensource.vercel.app/learn/start-a-new-react-project#building-with-a-full-featured-framework)提供比直接在组件中编写效果更有效的内置数据获取机制的原因。**

如果你不使用框架（并且不想构建自己的框架）但希望从 Effects 中获取数据更符合人体工程学，请考虑将获取逻辑提取到自定义 Hook 中，如下例所示：

```jsx
function SearchResults({ query }) {
  const [page, setPage] = useState(1); 
  const params = new URLSearchParams({ query, page });
  const results = useData(`/api/search?${params}`);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}

function useData(url) {
  const [result, setResult] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setResult(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [url]);
  return result;
}
```

你可能还想为错误处理添加一些逻辑并跟踪内容是否正在加载。你可以自己构建这样的 Hook，也可以使用 React 生态系统中已有的众多解决方案之一。**尽管仅此一项不会像使用框架内置的数据获取机制那样高效，但将数据获取逻辑移动到自定义 Hook 将更容易在以后采用高效的数据获取策略。**

一般来说，每当你必须求助于编写效果时，请留意何时可以将一段功能提取到自定义 Hook 中，并使用更具声明性和专门构建的 API（如上面的 `useData`）。组件中的原始 `useEffect` 调用越少，维护应用程序就越容易。

## 回顾

-   如果你可以在渲染期间计算某些内容，则不需要 Effect。
-   要缓存昂贵的计算，请添加使用 `useMemo` 而不是 `useEffect`。
-   要重置整个组件树的状态，可以传递一个不同的 `Key`。
-   要重置特定的状态位以响应 prop 的更改，请在渲染期间设置它。
-   由于显示组件而需要运行的代码应位于 Effects 中，其余代码应位于 Event 中。
-   如果需要更新多个组件的状态，最好在单个事件期间进行。
-   每当你尝试同步不同组件中的状态变量时，请考虑提升状态。
-   你可以使用 Effects 获取数据，但你需要实现清理以避免竞争条件。
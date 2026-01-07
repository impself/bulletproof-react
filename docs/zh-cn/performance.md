# 🚄 性能

### 代码分割（Code Splitting）

代码分割是指将生产环境的 JavaScript 拆分为更小的文件，以优化应用加载时间。通过这种方式，应用可以“按需下载”，只在需要时获取必要的代码。

理想情况下，代码分割应在路由层级实施，确保首屏只加载关键代码，其他部分按需懒加载。

需要注意避免过度代码分割：拆得太细会导致需要请求更多的 chunk，反而降低性能。应当策略性地分割，聚焦于应用的关键路径与核心模块，在性能优化与资源加载效率之间取得平衡。

[代码分割示例代码](../apps/react-vite/src/app/router.tsx)

### 组件与状态优化

- 不要把所有东西都塞进一个 state，这可能会触发不必要的重渲染。更好的方式是根据使用位置将全局状态拆分成多个状态。

- 将状态尽可能放在最靠近使用它的地方。这可以避免那些不依赖该状态的组件在更新时被连带重渲染。

- 如果某个 state 的初始值来自一次昂贵的计算，优先使用 state 初始化函数，而不要在渲染过程中直接执行昂贵函数。因为初始化函数只会在首次渲染时执行一次，例如：

```javascript
// instead of this which would be executed on every re-render:
const [state, setState] = React.useState(myExpensiveFn());

// prefer this which is executed only once:
const [state, setState] = React.useState(() => myExpensiveFn());
```

- 如果你的应用需要在同一个 state 中跟踪大量元素，并且希望更细粒度地更新，可以考虑支持原子更新（atomic updates）的状态管理库，例如 [jotai](https://jotai.pmnd.rs/)。

- 谨慎使用 React Context。Context 更适合低频变化的数据，例如主题、用户信息、小型本地状态等。对于中/高频变化的数据，可以考虑使用支持 selector 的方案，例如 [use-context-selector](https://github.com/dai-shi/use-context-selector)（而 selector 在常见的状态管理库中通常是内置的，例如 [zustand](https://docs.pmnd.rs/zustand/getting-started/introduction) 或 [jotai](https://jotai.org/)）。

  需要记住：Context 常被视为解决 props drilling 的“万金油”，但很多时候你可以通过 [提升状态](https://react.dev/learn/sharing-state-between-components#lifting-state-up-by-example) 或 [更合理的组件组合](https://react.dev/learn/passing-data-deeply-with-context#before-you-use-context) 来满足需求。不要急着上 Context 和全局状态。

- 如果你的应用预计会频繁更新、并可能影响性能，可以考虑从运行时样式方案（例如 [emotion](https://emotion.sh/docs/introduction)、[styled-components](https://styled-components.com/)——它们会在运行时生成样式）迁移到零运行时样式方案（例如 [tailwind](https://tailwindcss.com/)、[vanilla-extract](https://github.com/seek-oss/vanilla-extract)、[CSS modules](https://github.com/css-modules/css-modules)——它们在构建时生成样式）。

### 使用 children 作为最基础的优化

- `children` prop 是最基础、最容易实施的组件优化方式之一。正确使用时，它能消除很多不必要的重渲染。

  以 `children` 形式传入的 JSX 表示一个相对独立的 VDOM 结构，它不需要（也无法）被父组件在自身状态更新时重新渲染。示例：

```javascript
// Not optimized example
const App = () => <Counter />;

const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((count) => count + 1)}>
        count is {count}
      </button>
      <PureComponent /> // will rerender whenever "count" updates
    </div>
  );
};

const PureComponent = () => <p>Pure Component</p>;

// Optimized example
const App = () => (
  <Counter>
    <PureComponent />
  </Counter>
);

const Counter = ({ children }) => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((count) => count + 1)}>
        count is {count}
      </button>
      {children} // won't rerender whenever "count" updates
    </div>
  );
};

const PureComponent = () => <p>Pure Component</p>;
```

### 图片优化

- 对不在视口内的图片进行懒加载（lazy loading）。
- 使用 WEBP 等现代图片格式以加快加载。
- 使用 `srcset` 为不同屏幕尺寸加载更合适的图片。

### Web Vitals

自从 Google 将 Web Vitals 纳入网站索引评估后，你应该持续关注 Web Vitals 分数，例如通过 [Lighthouse](https://web.dev/measure/) 和 [Pagespeed Insights](https://pagespeed.web.dev/)。

### 数据预取（Prefetching）

你可以在用户导航到页面之前提前预取数据。例如使用 `@tanstack/react-query` 的 `queryClient.prefetchQuery` 方法为某个 query 预取数据。

当你预计用户将要进入某个页面时，提前预取可以降低进入页面后的等待时间，从而提升体验。

[数据预取示例代码](../apps/react-vite/src/features/discussions/components/discussions-list.tsx)

# 🗃️ 状态管理

有效管理状态对优化应用性能至关重要。与其把所有状态信息都放在一个中心化的单一仓库里，更推荐根据使用方式将状态划分为不同类别。

通过对状态进行分类，你可以简化状态管理流程，并提升应用整体效率。

## 组件状态（Component State）

组件状态只与单个组件相关，不应被全局共享。必要时可以通过 props 传递给子组件。

通常你应该先把状态定义在组件内部；只有在应用其他位置也需要用到时，再考虑将状态“提升”到更高层级。

管理组件状态时，常用的 React hooks 包括：

- [useState](https://react.dev/reference/react/useState) - 适用于简单且相互独立的状态
- [useReducer](https://react.dev/reference/react/useReducer) - 适用于更复杂的状态：一次 action 需要更新多个状态片段时

[组件状态示例代码](../apps/react-vite/src/components/layouts/dashboard-layout.tsx)

## 应用状态（Application State）

应用状态用于管理全局部分，例如：全局弹窗、通知、主题/配色模式切换等。

为了更好的性能与可维护性，建议将状态尽可能局部化，让它靠近真正需要它的组件。

不要一开始就把所有状态都全局化，以免架构变得混乱。

常见的应用状态方案：

- [context](https://react.dev/learn/passing-data-deeply-with-context) + [hooks](https://react.dev/reference/react-dom/hooks)
- [redux](https://redux.js.org/) + [redux toolkit](https://redux-toolkit.js.org/)
- [mobx](https://mobx.js.org)
- [zustand](https://github.com/pmndrs/zustand)
- [jotai](https://github.com/pmndrs/jotai)
- [xstate](https://xstate.js.org/)

[全局状态示例代码](../apps/react-vite/src/components/ui/notifications/notifications-store.ts)

## 服务端缓存状态（Server Cache State）

服务端缓存状态指：从服务端获取的数据在客户端本地缓存，以便后续复用。

虽然可以把远程数据缓存到 Redux 等状态库中，但通常有更合适的解决方案。建议采用专门的数据请求/缓存工具，以提升性能并优化数据获取流程。

常见的服务端缓存库：

- [react-query](https://tanstack.com/query) - REST + GraphQL
- [swr](https://swr.vercel.app/) - REST + GraphQL
- [apollo client](https://www.apollographql.com/) - GraphQL
- [urql](https://formidable.com/open-source/urql/) - GraphQL
- [RTK](https://redux-toolkit.js.org/rtk-query)

[服务端缓存状态示例代码](../apps/react-vite/src/features/discussions/api/get-discussions.ts)

## 表单状态（Form State）

表单是应用的重要组成部分，管理好表单状态对用户体验很关键。

处理表单状态时，可以考虑使用 Formik、React Hook Form 或 Final Form 等库来简化流程。这些库通常内置校验、错误处理与提交能力。

React 中的表单既可以是 [受控或非受控](https://react.dev/learn/sharing-state-between-components#controlled-and-uncontrolled-components)。

根据应用需要，表单可能会变得很复杂，包含很多字段与校验逻辑。

虽然仅用 React 原语也能实现任意表单，但以下是一些成熟方案：

- [React Hook Form](https://react-hook-form.com/)
- [Formik](https://formik.org/)
- [React Final Form](https://github.com/final-form/react-final-form)

建议抽象一个 `Form` 组件，并把各种输入组件封装为适配你应用需求的实现，以统一使用方式。

[Form 示例代码](../apps/react-vite/src/components/ui/form/form.tsx)

[输入组件示例代码](../apps/react-vite/src/components/ui/form/input.tsx)

你也可以将校验库与上述方案集成，在客户端校验输入，例如：

- [zod](https://github.com/colinhacks/zod)
- [yup](https://github.com/jquense/yup)

[校验示例代码](../apps/react-vite/src/features/auth/components/register-form.tsx)

## URL 状态（URL State）

URL 状态指存储在浏览器地址栏并可被操控的数据。

通常通过路径参数（例如 `/app/${dynamicParam}`）或查询参数（例如 `/app?dynamicParam=1`）来管理。

通过 react-router-dom 等路由方案，你可以访问与控制 URL 状态，从而让应用参数能够直接在地址栏中动态变化。

[URL 状态示例代码](../apps/react-vite/src/features/discussions/components/discussion-view.tsx)

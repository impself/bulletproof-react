# 📡 API 层

### 使用单一的 API Client 实例

当你的应用需要与 REST 或 GraphQL API 交互时，建议使用一个预先配置好的 API 客户端单例，并在整个应用中复用它。比如，你可以基于原生 `fetch` API 创建一个 API Client，或者使用 [axios](https://github.com/axios/axios)、[graphql-request](https://github.com/prisma-labs/graphql-request)、[apollo-client](https://www.apollographql.com/docs/react/) 等库，并提前设置好通用配置。

[API Client 示例代码](../apps/react-vite/src/lib/api-client.ts)

### 定义并导出请求声明

不要在使用时临时拼装 API 请求，更推荐将请求声明以独立、可复用的方式定义并导出。

以结构化方式声明 API 请求可以帮助保持代码库的整洁和可维护性，因为相关内容会被集中（colocate）在一起。
每个 API 请求声明应包含：

- 请求与响应数据的类型定义与校验 schema
- 使用 API Client 实例调用端点的 fetcher 函数
- 基于 fetcher 的 hook（通常构建在 [react-query](https://tanstack.com/query)、[swr](https://swr.vercel.app/)、[apollo-client](https://www.apollographql.com/docs/react/)、[urql](https://formidable.com/open-source/urql/) 等库之上），用于管理数据请求与缓存逻辑

这种方式能让你更容易追踪应用中已定义的所有端点。同时，对响应进行类型化并在应用中继续推导，也能提升整体的类型安全。

[API 请求声明 - Query - 示例代码](../apps/react-vite/src/features/discussions/api/get-discussions.ts)
[API 请求声明 - Mutation - 示例代码](../apps/react-vite/src/features/discussions/api/create-discussion.ts)

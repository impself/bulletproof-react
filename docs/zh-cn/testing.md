# 🧪 测试

正如这条 [tweet](https://twitter.com/rauchg/status/807626710350839808) 所强调的：测试的有效性很大程度上来自集成测试与端到端（e2e）测试带来的覆盖度。

单元测试确实能隔离并验证单个组件/函数，但真正能带来对应用功能的信心，往往来自扎实的集成测试与 e2e 测试策略。

## 测试类型

### 单元测试（Unit Tests）

单元测试是你能写的最小粒度测试，用于在隔离环境中测试应用的单个部分。它适合测试全局共享组件、通用函数，以及单个组件内部较复杂的逻辑。

单元测试运行快、编写成本相对低。

[单元测试示例代码](../apps/react-vite/src/components/ui/dialog/confirmation-dialog/__tests__/confirmation-dialog.test.tsx)

### 集成测试（Integration Tests）

集成测试用于验证应用中不同部分如何协同工作。

在大多数情况下，应把测试重点放在集成测试上，因为它们更能提升对应用可靠性的信心。

单元测试通过并不代表应用一定正常：如果模块之间的衔接有问题，应用仍可能无法正确工作。

因此，对不同 feature 进行集成测试非常关键，它能确保应用稳定、连贯地运行。

[集成测试示例代码](../apps/react-vite/src/app/routes/app/discussions/__tests__/discussion.test.tsx)

### 端到端测试（E2E）

端到端测试是一种以整体系统为单位的测试方法。它会自动化运行整个应用（包含前端与后端），以验证整个系统是否按预期工作。

E2E 测试会模拟真实用户与应用交互的完整流程。

[E2E 示例代码](../apps/react-vite/e2e/tests/smoke.spec.ts)

## 推荐工具

#### [Vitest](https://vitest.dev)

Vitest 是一个强大的测试框架，能力与 Jest 类似，但更贴合现代工具链，更新更及时。它可定制性强、灵活度高，是测试 JavaScript/TypeScript 的热门选择。

#### [Testing Library](https://testing-library.com/)

Testing Library 是一组让测试更容易的工具与库。

它的核心理念是：以真实用户的使用方式来测试应用，而不是测试实现细节。

例如，不要测试组件当前 state 的值，而应测试用户在屏幕上看到的输出。

这样即使你将状态管理方案替换了，测试仍然具备价值，因为用户看到的结果不应变化。

#### [Playwright](https://playwright.dev)

Playwright 是自动化运行 e2e 测试的工具。

你会定义真实用户在应用中执行的一系列操作步骤，然后启动测试。它可以在两种模式运行：

- Browser mode - 打开一个专用浏览器，从头到尾运行应用。你会获得一套很好的可视化与检查工具来观察每一步。因为开销更大，通常只在本地开发时使用。
- Headless mode - 启动无界面浏览器运行测试，非常适合在 CI/CD 中集成，在每次部署时执行。

#### [MSW](https://mswjs.io)

在原型阶段，可以用 MSW 来模拟 API。它能让你在不依赖真实后端的情况下快速构建前端。

MSW 并不是实际后端，而是运行在 Service Worker 中的 mocked server：它会拦截所有 HTTP 请求，并按你定义的 handlers 返回对应响应。

当你只有前端权限、或后端功能还没完成时，这非常有用：你不必等待后端完成，也不需要在代码中硬编码数据，而是使用真实的 HTTP 调用来构建前端功能。

MSW 也适合用于 API 设计：业务逻辑可以在 handlers 中实现。

[API Handlers 示例代码](../apps/react-vite/src/testing/mocks/handlers/auth.ts)

[数据模型示例代码](../apps/react-vite/src/testing/mocks/db.ts)

拥有一个可用的 mocked API server 也非常利于测试：你不必 mock fetch，而是直接向 mocked server 发请求，并返回应用期望的数据。

# 🔐 安全

## 认证（Auth）

注意：在客户端管理认证固然重要，但同样需要在服务端实现健壮的安全措施来保护资源。客户端认证能提升用户体验，并与服务端安全相辅相成。

资源保护包含两个关键部分：

### Authentication（身份认证）

身份认证是验证用户身份的过程。在单页应用（SPA）中，最常见的认证方式是 JSON Web Token（[JWT](https://jwt.io/)）。当用户登录或注册时，服务端返回 token，应用将其存储起来。之后每次需要认证的请求，都会在 header 或 cookie 中携带 token，以验证用户身份与访问权限。

最安全的做法是将 token 存在应用状态（state）中。但需要注意：一旦用户刷新应用，state 会被重置，从而导致认证状态丢失。

因此 token 通常需要存储在 cookie 或 `localStorage/sessionStorage` 中。

#### 用 `localStorage` 还是 cookie 来存 token？

把认证 token 存在 localStorage 存在安全风险，尤其在存在跨站脚本攻击（[XSS](https://owasp.org/www-community/attacks/xss/)）漏洞时，恶意脚本可能窃取 token。

将 token 存在 cookie 中，并配合 `HttpOnly` 属性，可以提升安全性，因为客户端 JavaScript 无法访问 HttpOnly cookie。

在示例应用中，我们使用 js-cookie 管理 cookie，并假设真实 API 会强制设置 HttpOnly 属性以增强安全性，从而让前端无法直接读取 cookie。

除了安全地存储 token，还需要保护整个应用免受 XSS 攻击。一个关键策略是：在把用户输入渲染到页面之前，对其进行消毒/净化（sanitize）。通过严格净化输入，可以降低 XSS 风险，提升应用对恶意攻击的抵抗力。

[HTML 消毒示例代码](../apps/react-vite/src/components/ui/md-preview/md-preview.tsx)

更多安全风险可参考 [OWASP](https://owasp.org/www-project-top-10-client-side-security-risks/)。

#### 处理用户数据

用户信息应当被视为全局状态的一部分，且在应用中任何位置都可访问。

如果你已经使用了 `react-query`，可以考虑用 [react-query-auth](https://github.com/alan2207/react-query-auth) 来管理用户状态，它在你提供配置后会帮你处理大部分事情。

否则，你也可以使用 React context + hooks，或使用第三方状态管理库。

用户信息应当作为应用中的核心数据全局可用。如果你在使用 `react-query`，也可以用它来存储用户数据；否则可用 React context + hooks 或第三方状态库。

[Auth 配置示例代码](../apps/react-vite/src/lib/auth.tsx)

当应用中存在 user 对象时，将默认视为用户已认证。

### Authorization（授权）

授权是验证用户是否有权限访问应用中某个特定资源的过程。

#### RBAC（基于角色的访问控制）

[授权配置示例代码](../apps/react-vite/src/lib/authorization.tsx)

在基于角色的授权模型中，通过定义角色并为其分配权限来控制资源访问。例如 `USER` 与 `ADMIN` 可对应不同的权限范围：普通用户受限于部分能力，而管理员可以访问全部功能。

[RBAC 示例代码](../apps/react-vite/src/features/discussions/components/create-discussion.tsx)

#### PBAC（基于权限/策略的访问控制）

虽然 RBAC 提供了结构化的授权方式，但有时需要更细粒度的控制。PBAC 提供更灵活的方案，尤其适用于按条件精确控制权限的场景，例如“只有资源所有者才能执行某操作”。

以评论为例，PBAC 可以确保只有评论作者有权限删除该评论，从而提供更精细、更可定制的访问控制。

如果你只需要 RBAC 保护，可以使用 `RBAC` 组件并传入允许的角色；如果需要更严格的保护，可以传入策略校验。

[PBAC 示例代码](../apps/react-vite/src/features/comments/components/comments-list.tsx)

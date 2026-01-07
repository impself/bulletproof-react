# ⚙️ 项目规范

在 React 应用中强制执行项目规范对于保持代码质量、一致性与可扩展性至关重要。通过建立并遵循一套最佳实践，团队可以确保代码库保持干净、组织良好且易于维护。

#### ESLint

ESLint 是 JavaScript 的重要 lint 工具，用于维护代码质量并约束编码规范。通过在 `.eslintrc.js` 中配置规则，ESLint 能识别并避免常见错误，提升代码正确性，并促进全项目的一致性。

这不仅能帮助尽早发现问题，也能强化统一的编码方式，从而提升整体质量与可读性。

[ESLint 配置示例代码](../apps/react-vite/.eslintrc.cjs)

#### Prettier

Prettier 用于保持一致的代码格式。你可以在 IDE 中启用 “format on save” 功能，让代码在保存时自动按 `.prettierrc` 的规则格式化。

这种做法能确保整个代码库的风格统一，并在出现格式化失败时提示潜在的语法错误。此外，也可以将 Prettier 与 ESLint 集成，让格式化与规范校验协同工作。

[Prettier 配置示例代码](../apps/react-vite/.prettierrc)

#### TypeScript

ESLint 能有效检测 JavaScript 中的语言层面问题。但由于 JavaScript 动态特性，ESLint 并不总能捕获运行时数据问题，尤其在复杂项目中更是如此。因此推荐使用 TypeScript。

TypeScript 在大型重构中非常有价值，它能在重构过程中更早发现潜在问题。重构时可以优先更新类型声明，然后逐步修复项目中的 TypeScript 报错。

需要注意：TypeScript 通过构建时类型检查提升开发信心，但并不能完全阻止运行时失败。这里有一份很好的资源：[在 React 中使用 TypeScript](https://react-typescript-cheatsheet.netlify.app/)。

#### Husky

Husky 用于在工作流中实现并执行 git hooks。通过在每次提交前运行代码校验，你可以确保代码质量，避免将有问题的提交推送到仓库。

Husky 可用于在 push/commit 前执行 lint、格式化、类型检查等任务。配置方式可参考：[Husky 使用文档](https://typicode.github.io/husky/#/?id=usage)。

#### 绝对导入（Absolute imports）

建议始终配置并使用绝对导入（absolute imports），这会让文件移动更容易，并避免 `../../../component` 这种混乱的相对路径。

无论你把文件移动到哪里，导入路径都更稳定。以下是配置方式：

JavaScript（`jsconfig.json`）项目：

```json
"compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
```

TypeScript（`tsconfig.json`）项目：

```json
"compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
```

也可以为不同目录定义多个别名（如 `@components`、`@hooks` 等），但使用 `@/*` 通常足够：足够短，不需要配置多套路径；同时与 `node_modules` 依赖模块区分明显，不易混淆。

这意味着 `src` 下的任何内容都可以通过 `@` 访问。例如 `src/components/my-component` 可以用 `@/components/my-component` 来导入，而不是 `../../../components/my-component`。

#### 文件命名约定

你还可以在项目中强制文件命名与目录命名规范。例如，规定所有文件都使用 `kebab-case`，这能让代码库更一致、更好导航。

可以通过 ESLint 强制：

```js
'check-file/filename-naming-convention': [
  'error',
  {
      '**/*.{ts,tsx}': 'KEBAB_CASE',
  },
  {
      // ignore the middle extensions of the filename to support filename like bable.config.js or smoke.spec.ts
      ignoreMiddleExtensions: true,
  },
],
'check-file/folder-naming-convention': [
  'error',
  {
    // all folders within src (except __tests__)should be named in kebab-case
    'src/**/!(__tests__)': 'KEBAB_CASE',
  },
],
```

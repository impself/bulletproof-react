# 🗄️ 项目结构

大部分代码位于 `src` 目录，结构大致如下：

```sh
src
|
+-- app               # 应用层，包含：
|   |                 # 该目录可能会因所使用的元框架（meta framework）而不同
|   +-- routes        # 应用路由 / 在某些框架中也可能是 pages
|   +-- app.tsx       # 主应用组件
|   +-- provider.tsx  # 应用 Provider：用不同的全局 provider 包裹整个应用（同样可能因框架而不同）
|   +-- router.tsx    # 路由配置
+-- assets            # 静态资源：图片、字体等
|
+-- components        # 全局共享组件
|
+-- config            # 全局配置、导出的环境变量等
|
+-- features          # 按功能划分的模块
|
+-- hooks             # 全局共享 hooks
|
+-- lib               # 为应用预配置的可复用库
|
+-- stores            # 全局状态 stores
|
+-- testing           # 测试工具与 mock
|
+-- types             # 全局共享类型
|
+-- utils             # 全局共享工具函数
```

为了便于扩展与维护，建议将大多数业务代码组织在 `features` 目录中。每个 feature 目录只包含该功能相关的代码，使关注点清晰分离。

与把所有文件平铺在一个目录相比，这种方式更易于协作、阅读与规模化扩展。

一个 feature 可以采用如下结构：

```sh
src/features/awesome-feature
|
+-- api         # 与该 feature 相关的 API 请求声明与 hooks
|
+-- assets      # 该 feature 的静态资源
|
+-- components  # 仅在该 feature 范围内使用的组件
|
+-- hooks       # 仅在该 feature 范围内使用的 hooks
|
+-- stores      # 该 feature 的状态 stores
|
+-- types       # feature 内部使用的 TypeScript 类型
|
+-- utils       # feature 内部使用的工具函数
```

注意：并不是每个 feature 都需要包含以上所有子目录，只保留实际需要的即可。

在某些情况下，把所有 API 调用集中放在一个单独的 `api` 目录（而不是放在 feature 目录中）会更实用，尤其当多个 feature 之间共享大量 API 调用时。

过去曾经推荐使用 barrel files 来统一导出 feature 内所有文件，但这可能会影响 Vite 的 tree shaking，带来性能问题。因此更推荐直接按需导入具体文件。

通常不建议在 feature 之间互相导入。更好的方式是在应用层进行组合（compose）不同的 feature，从而保持每个 feature 的独立性，避免代码库变得复杂难懂。

要禁止跨 feature 导入，可以通过 ESLint：

```js
'import/no-restricted-paths': [
    'error',
    {
        zones: [
            // disables cross-feature imports:
            // eg. src/features/discussions should not import from src/features/comments, etc.
            {
                target: './src/features/auth',
                from: './src/features',
                except: ['./auth'],
            },
            {
                target: './src/features/comments',
                from: './src/features',
                except: ['./comments'],
            },
            {
                target: './src/features/discussions',
                from: './src/features',
                except: ['./discussions'],
            },
            {
                target: './src/features/teams',
                from: './src/features',
                except: ['./teams'],
            },
            {
                target: './src/features/users',
                from: './src/features',
                except: ['./users'],
            },

            // More restrictions...
        ],
    },
],
```

你可能还想进一步强制单向的代码架构（unidirectional architecture）：代码应当从共享层流向应用层（shared -> features -> app）。这能让代码库更可预测、更容易理解。

![单向代码架构](../assets/unidirectional-codebase.png)

如图所示：共享模块可以被代码库的任何部分使用；feature 只能从共享模块导入；app 则可以从 feature 与共享模块导入。

要强制这一点，可以通过 ESLint：

```js
'import/no-restricted-paths': [
    'error',
    {
    zones: [
        // Previous restrictions...

        // enforce unidirectional codebase:
        // e.g. src/app can import from src/features but not the other way around
        {
            target: './src/features',
            from: './src/app',
        },

        // e.g src/features and src/app can import from these shared modules but not the other way around
        {
            target: [
                './src/components',
                './src/hooks',
                './src/lib',
                './src/types',
                './src/utils',
            ],
            from: ['./src/features', './src/app'],
        },
    ],
    },
],
```

通过遵循这些实践，你可以确保代码库组织清晰、可扩展且易于维护，帮助团队更高效地协作。

这种架构思路也更容易迁移到使用 Next.js、Remix 或 React Native 构建的应用中。

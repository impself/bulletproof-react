# 💻 应用概览

这个示例应用相对简单：用户可以创建团队，其他用户可以加入；团队成员可以就不同话题彼此发起讨论。

如果用户在注册时没有选择加入已有团队，则会在注册过程中创建一个团队，并且该用户成为团队管理员。

[演示地址](https://bulletproof-react-app.netlify.app)

## 数据模型

应用包含以下模型：

- User（用户）— 可拥有以下角色之一：

  - `ADMIN` 可以：
    - 创建/编辑/删除讨论
    - 创建/删除所有评论
    - 删除用户
    - 编辑自己的资料
  - `USER` 可以：
    - 编辑自己的资料
    - 创建/删除自己的评论

- Team（团队）：代表一个团队，包含 1 位管理员与多位用户，团队成员之间可以参与讨论。

- Discussion（讨论）：代表由团队成员创建的讨论主题。

- Comment（评论）：代表讨论中的所有消息。

## 快速开始

要开始运行，请查看你想启动的应用目录下的 README.md。

- [React Vite](../apps/react-vite/README.md)
- [Next.js App Router](../apps/nextjs-app/README.md)
- [Next.js Pages](../apps/nextjs-pages/README.md)

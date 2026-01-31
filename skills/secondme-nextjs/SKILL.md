---
name: secondme-nextjs
description: 引导用户使用 Next.js 构建与 SecondMe API 集成的全栈项目
user-invocable: true
---

# SecondMe Next.js 全栈集成开发

## 概述

这是一个引导用户使用 Next.js 构建与 SecondMe API 集成的全栈项目的工作流 skill。采用 OAuth2 标准授权流程，确保安全可靠的用户授权体验。

---

## 前端设计要求

**重要：** 在构建前端界面时，必须使用 `/frontend-design` skill 来生成高质量的 UI 组件。

设计原则：
- **亮色主题**：仅使用亮色/浅色主题，不使用暗色/深色主题
- **简约优雅**：遵循极简设计理念，减少视觉噪音
- **产品特性驱动**：UI 设计应紧密结合要实现的功能特性
- **现代感**：采用当下流行的设计趋势，避免过时的 UI 模式
- **一致性**：保持整体视觉风格统一
- **响应式**：适配各种屏幕尺寸
- **中文界面**：所有用户可见的文字（按钮、提示、标签、说明等）必须使用中文
- **稳定优先**：避免复杂动画效果，仅使用简单的过渡动画（如 hover、fade），确保界面稳定流畅

---

## 工作流程

### 第一步：查阅官方文档

在开始开发之前，请先查阅 SecondMe API 的官方文档，了解 API 的功能和使用方式。

**文档地址：** https://develop-docs.second.me/zh/docs

**核心文档列表：**

| 文档 | 地址 | 说明 |
|------|------|------|
| 快速入门 | https://develop-docs.second.me/zh/docs | API 概览和快速开始 |
| 认证概述 | https://develop-docs.second.me/zh/docs/authentication | OAuth2 认证方式说明 |
| OAuth2 指南 | https://develop-docs.second.me/zh/docs/authentication/oauth2 | OAuth2 授权流程详解 |
| SecondMe API 参考 | https://develop-docs.second.me/zh/docs/api-reference/secondme | 完整的 API 端点文档 |
| OAuth2 API 参考 | https://develop-docs.second.me/zh/docs/api-reference/oauth | OAuth2 相关 API 端点 |
| 错误码参考 | https://develop-docs.second.me/zh/docs/errors | 错误码和处理方式 |

**请先阅读文档，了解以下内容：**
1. SecondMe API 提供哪些功能（用户信息、聊天、笔记等）
2. OAuth2 授权流程的完整步骤
3. API 的基础 URL 和请求格式

---

### 第二步：注册应用并获取 OAuth2 凭证

请按照以下步骤在 SecondMe 平台注册你的应用：

1. **登录 SecondMe 开发者后台**
2. **创建新应用**，获取：
   - Client ID
   - Client Secret
3. **配置回调地址（Redirect URI）**
   - 本地开发：`http://localhost:3000/api/auth/callback`
   - 生产环境：`https://your-domain.com/api/auth/callback`

---

### 第三步：提供配置信息

请提供以下 OAuth2 凭证信息：

```env
# SecondMe OAuth2 配置
SECONDME_CLIENT_ID=your_client_id
SECONDME_CLIENT_SECRET=your_client_secret
SECONDME_REDIRECT_URI=http://localhost:3000/api/auth/callback
```

---

### 第四步：确定功能需求

请描述你想要实现的产品功能。以下是 SecondMe API 支持的能力：

**用户信息相关：**
- 获取用户基础信息（昵称、头像等）
- 获取用户兴趣标签（Shades）
- 获取用户软记忆/知识库

**聊天相关：**
- 与 SecondMe AI 进行流式对话
- 获取历史会话列表
- 获取会话消息记录

**笔记相关：**
- 添加笔记/记忆到用户的 SecondMe

**常见产品形态示例：**
1. AI 智能聊天助手
2. 个人画像可视化展示
3. 知识库管理与探索工具
4. 第三方应用集成入口

---

### 第五步：开始开发

收集到以上信息后，我将按以下流程帮你构建项目：

1. **初始化 Next.js 项目**（使用 App Router）
2. **配置环境变量**
3. **实现 OAuth2 认证流程**
   - 授权跳转
   - 回调处理
   - Token 管理（存储、刷新）
4. **开发后端 API 路由**
   - 代理 SecondMe API
   - 处理认证状态
5. **使用 `/frontend-design` skill 构建前端界面**
   - 简约现代的 UI 设计
   - 紧密结合产品功能特性
   - 良好的用户体验
   - 所有界面文字使用中文
6. **错误处理和边界情况**

---

## 技术栈

- **框架：** Next.js 14+ (App Router)
- **语言：** TypeScript
- **样式：** Tailwind CSS
- **前端设计：** 使用 `/frontend-design` skill 生成
- **API 调用：** fetch
- **状态管理：** React hooks
- **运行端口：** 必须使用 3000 端口，不允许切换其他端口

---

## 常见问题与注意事项

### CSS @import 顺序问题

**问题：** 在 `globals.css` 中使用 `@import url(...)` 引入 Google Fonts 会导致构建失败。

**错误信息：**
```
@import rules must precede all rules aside from @charset and @layer statements
```

**原因：** Tailwind CSS 的 `@import "tailwindcss"` 展开后生成大量规则，导致后续的 `@import url(...)` 不再是第一个。

**正确做法：**

1. **使用 `<link>` 标签（推荐）：** 在 `layout.tsx` 中引入
   ```tsx
   <head>
     <link rel="preconnect" href="https://fonts.googleapis.com" />
     <link href="https://fonts.googleapis.com/css2?family=..." rel="stylesheet" />
   </head>
   ```

2. **使用 `next/font`（最佳实践）：**
   ```tsx
   import { Noto_Sans_SC } from 'next/font/google'
   const notoSans = Noto_Sans_SC({ subsets: ['latin'], weight: ['400', '500'] })
   ```

### 构建验证

**重要：** 每次完成代码修改后，必须运行以下命令验证构建：

```bash
npm run build
```

特别关注：
- CSS 解析错误
- TypeScript 类型错误
- 导入路径错误

---

## 开始

现在请回答以下问题，我将根据你的需求开始构建项目：

1. **你已经在 SecondMe 平台注册应用了吗？** 如果是，请提供 Client ID 和 Client Secret（可以使用占位符，稍后配置）
2. **你想实现什么功能？** 请详细描述你的产品想法和核心功能
3. **有没有特别的设计偏好？** 比如配色风格、布局方式等

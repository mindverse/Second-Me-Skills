# Skills Interface Contracts

**Feature**: 001-secondme-skills-refactor
**Date**: 2026-02-05

## 概述

本文档定义 SecondMe Skills 的接口契约，包括每个 skill 的输入、输出、前置条件和副作用。

---

## 1. /secondme-init

### 元数据

```yaml
name: secondme-init
description: 初始化 SecondMe 项目配置和功能模块选择
user-invocable: true
```

### 接口定义

| 属性 | 值 |
|------|-----|
| **触发方式** | `/secondme-init` |
| **参数** | 无 |
| **前置条件** | 无 |
| **副作用** | 创建 `.secondme/state.json`，创建或更新 `CLAUDE.md` |

### 输入

通过 AskUserQuestion 收集：

1. **Client ID** (必填)
   - 类型: 文本输入
   - 验证: 非空

2. **Client Secret** (必填)
   - 类型: 文本输入
   - 验证: 非空

3. **Redirect URI** (可选)
   - 类型: 文本输入
   - 默认值: `http://localhost:3000/api/auth/callback`

4. **Database URL** (必填)
   - 类型: 文本输入
   - 验证: 有效连接串格式

5. **功能模块** (必填)
   - 类型: 多选
   - 选项: auth (必选), profile, chat, note
   - 默认值: [auth]

### 输出

成功时：
```
✅ SecondMe 项目配置已完成！

已保存配置到 .secondme/state.json
已选择模块: auth, chat
已创建/更新 CLAUDE.md（包含 SecondMe API 文档链接）

⚠️  请将 .secondme/ 添加到 .gitignore 以保护敏感信息

下一步：
- 运行 /secondme-prd 定义产品需求
- 或运行 /secondme-nextjs --quick 快速生成项目
```

### CLAUDE.md 生成内容

```markdown
# SecondMe 集成项目

## API 文档

开发时请参考以下官方文档：

| 文档 | 地址 |
|------|------|
| 快速入门 | https://develop-docs.second.me/zh/docs |
| OAuth2 认证 | https://develop-docs.second.me/zh/docs/authentication/oauth2 |
| API 参考 | https://develop-docs.second.me/zh/docs/api-reference/secondme |
| 错误码 | https://develop-docs.second.me/zh/docs/errors |

## 关键信息

- API 基础 URL: `https://app.mindos.com/gate/lab`
- OAuth 授权 URL: `https://go.second.me/oauth/`
- Access Token 有效期: 2 小时
- Refresh Token 有效期: 30 天

## 已选模块

[根据 state.json 中的 modules 动态生成]
```

失败时：
```
❌ 配置验证失败：[具体错误信息]
```

### 状态变更

| 字段 | 变更 |
|------|------|
| stage | 设为 "init" |
| modules | 设为用户选择 |
| config | 设为用户输入 |

---

## 2. /secondme-prd

### 元数据

```yaml
name: secondme-prd
description: 通过对话定义产品需求，根据已选模块针对性提问
user-invocable: true
```

### 接口定义

| 属性 | 值 |
|------|-----|
| **触发方式** | `/secondme-prd` |
| **参数** | 无 |
| **前置条件** | state.json 存在且 stage >= "init" |
| **副作用** | 更新 `.secondme/state.json` 的 prd 字段和 stage |

### 前置检查

```
检查 .secondme/state.json:
- 文件存在 → 继续
- 文件不存在 → 错误: 请先运行 /secondme-init

检查 stage:
- stage == "init" → 继续
- stage == "prd" or "ready" → 询问: 是否重新定义需求？
```

### 对话流程

**第一轮：展示 API 能力**

基于已选模块，展示相关 API 能力：
- auth: 用户登录、Token 管理
- profile: 获取用户信息、兴趣标签
- chat: 流式对话、会话历史
- note: 添加笔记

**第二轮：收集核心需求**

提问：
1. 你的应用主要解决什么问题？
2. 目标用户是谁？

**第三轮：功能细化**

基于模块针对性提问：
- chat 模块: 是否需要保存会话历史？对话界面偏好？
- profile 模块: 展示哪些用户信息？
- note 模块: 什么场景下添加笔记？

**第四轮：设计偏好**

提问：
- 界面风格偏好？（简约/华丽/卡通等）
- 配色偏好？

**第五轮：确认总结**

展示收集的需求摘要，确认后保存。

### 输出

成功时：
```
✅ 产品需求已定义！

概要: [summary]
功能列表:
- [feature 1]
- [feature 2]
设计偏好: [design_preference]

已保存到 .secondme/state.json

下一步：运行 /secondme-nextjs 生成项目
```

### 状态变更

| 字段 | 变更 |
|------|------|
| stage | 设为 "prd" |
| prd.summary | 设为需求摘要 |
| prd.features | 设为功能列表 |
| prd.design_preference | 设为设计偏好 |

---

## 3. /secondme-nextjs

### 元数据

```yaml
name: secondme-nextjs
description: 基于配置和需求生成 Next.js 项目，支持 --quick 快速模式
user-invocable: true
argument-hint: [--quick]
```

### 接口定义

| 属性 | 值 |
|------|-----|
| **触发方式** | `/secondme-nextjs` 或 `/secondme-nextjs --quick` |
| **参数** | `--quick` (可选) |
| **前置条件** | 见下方 |
| **副作用** | 生成 Next.js 项目文件，更新 stage 为 "ready" |

### 前置检查

```
检查 .secondme/state.json:
- 文件存在 → 继续
- 文件不存在 → 错误: 请先运行 /secondme-init

检查参数:
- 包含 --quick → 跳过 stage 检查
- 不包含 --quick → 检查 stage >= "prd"

stage 不满足时:
→ 错误: 请先运行 /secondme-prd 或使用 --quick 参数
```

### 生成内容

基于 modules 生成对应代码：

| 模块 | 生成内容 |
|------|----------|
| auth | OAuth 路由、User 表、登录组件 |
| profile | 用户信息 API、个人资料组件 |
| chat | 聊天 API、Session 表、聊天界面 |
| note | 笔记 API |

### 项目结构

```
project/
├── .env.local           # 从 config 生成
├── prisma/
│   └── schema.prisma    # 从 modules 生成
├── src/
│   └── app/
│       ├── api/
│       │   └── auth/
│       │       ├── login/route.ts
│       │       └── callback/route.ts
│       └── page.tsx
├── package.json
└── ...
```

### 输出

成功时：
```
✅ Next.js 项目已生成！

项目目录: ./[project-name]
已生成模块: auth, chat
数据库: PostgreSQL

启动步骤:
1. cd [project-name]
2. npm install
3. npx prisma db push
4. npm run dev

项目将在 http://localhost:3000 启动
```

### 状态变更

| 字段 | 变更 |
|------|------|
| stage | 设为 "ready" |

---

## Skills 依赖关系

```
                    ┌──────────────────┐
                    │ secondme-init    │
                    │ (入口)           │
                    │ → state.json     │
                    │ → CLAUDE.md      │
                    └────────┬─────────┘
                             │
              ┌──────────────┴──────────────┐
              ▼                             ▼
    ┌──────────────────┐          ┌──────────────────┐
    │ secondme-prd     │          │ secondme-nextjs  │
    │ (需求对话)        │          │ --quick          │
    └────────┬─────────┘          └────────┬─────────┘
             │                             │
             ▼                             │
    ┌──────────────────┐                   │
    │ secondme-nextjs  │◄──────────────────┘
    │ (项目生成)        │
    └──────────────────┘
```

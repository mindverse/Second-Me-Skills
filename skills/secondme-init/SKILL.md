---
name: secondme-init
description: 初始化 SecondMe 项目配置和功能模块选择，创建 state.json 和 CLAUDE.md
user-invocable: true
---

# SecondMe 项目初始化

初始化 SecondMe 项目配置，支持直接解析 App Info 格式或手动输入凭证。

**工具使用：** 收集用户输入时使用 `AskUserQuestion` 工具。

---

## 工作流程

### 第一步：检查现有配置

首先检查项目根目录是否存在 `.secondme/state.json`：

**如果存在**：
1. 读取并显示当前配置摘要：
   ```
   📋 已有配置：
   - App Name: secondme-tinder
   - Client ID: 71658da7-***
   - 数据库: postgresql://***
   - 已选模块: auth, chat, profile
   - 当前阶段: init
   ```
2. 询问用户是否要修改配置或继续使用

**如果不存在**：继续到第二步

---

### 第二步：收集配置信息

#### 方式一：解析 App Info 格式（推荐）

如果用户提供了以下格式的信息，自动解析：

```
## App Info
- App Name: secondme-tinder
- Client ID: 71658da7-659c-414a-abdf-cb6472037fc2
- Client Secret: <your-secret>
- Redirect URIs:
  - http://localhost:3000/api/auth/callback
  - https://xxx.vercel.app/api/auth/callback
- Allowed Scopes: user.info, user.info.shades, user.info.softmemory, chat, note.add, voice
```

**解析规则**：

| 字段 | 提取方式 |
|------|----------|
| `app_name` | 直接提取 App Name |
| `client_id` | 直接提取 Client ID |
| `client_secret` | 直接提取 Client Secret |
| `redirect_uri` | 优先选择 `localhost:3000` 的 URI（开发用） |
| `redirect_uris` | 保存所有 Redirect URIs 列表 |
| `modules` | 根据 Allowed Scopes 自动推断（见下表） |

**Scopes 到模块的映射**：

| Scope | 模块 |
|-------|------|
| `user.info` | `auth`（必选） |
| `user.info.shades` | `profile` |
| `user.info.softmemory` | `profile` |
| `chat` | `chat` |
| `note.add` | `note` |
| `voice` | 记录但暂不生成代码 |

**解析后只需额外收集**：
- **Database URL**（必填）

#### 方式二：手动输入

如果用户没有提供 App Info，则依次收集：

1. **App Name** (可选)
   - 提示：请输入应用名称
   - 默认值：`secondme-app`

2. **Client ID** (必填)
   - 提示：请输入 SecondMe 应用的 Client ID
   - 验证：非空

3. **Client Secret** (必填)
   - 提示：请输入 SecondMe 应用的 Client Secret
   - 验证：非空

4. **Redirect URI** (可选)
   - 提示：请输入回调地址
   - 默认值：`http://localhost:3000/api/auth/callback`

5. **Database URL** (必填)
   - 提示：请输入数据库连接串
   - 提供格式示例：
     - PostgreSQL: `postgresql://user:pass@localhost:5432/dbname`
     - MySQL: `mysql://user:pass@localhost:3306/dbname`
     - SQLite: `file:./dev.db`

6. **功能模块选择**（多选）

---

### 第三步：确认模块选择

展示根据 Scopes 推断的模块（或用户选择的模块）：

| 模块 | 说明 | 备注 |
|------|------|------|
| `auth` | OAuth 认证 | **必选**，自动包含 |
| `profile` | 用户信息展示 | 可选 |
| `chat` | 聊天功能 | 可选 |
| `note` | 笔记功能 | 可选 |

询问用户是否需要调整模块选择。

---

### 第四步：生成配置文件

#### 4.1 创建 `.secondme/state.json`

```json
{
  "version": "1.0",
  "stage": "init",
  "app_name": "secondme-tinder",
  "modules": ["auth", "chat", "profile"],
  "config": {
    "client_id": "71658da7-659c-414a-abdf-cb6472037fc2",
    "client_secret": "用户输入的 Client Secret",
    "redirect_uri": "http://localhost:3000/api/auth/callback",
    "redirect_uris": [
      "http://localhost:3000/api/auth/callback",
      "https://xxx.vercel.app/api/auth/callback"
    ],
    "database_url": "用户输入的数据库连接串",
    "allowed_scopes": ["user.info", "user.info.shades", "chat", "note.add"]
  },
  "api": {
    "base_url": "https://app.mindos.com/gate/lab",
    "oauth_url": "https://go.second.me/oauth/",
    "token_endpoint": "https://app.mindos.com/gate/lab/api/oauth/token",
    "access_token_ttl": 7200,
    "refresh_token_ttl": 2592000
  },
  "docs": {
    "quickstart": "https://develop-docs.second.me/zh/docs",
    "oauth2": "https://develop-docs.second.me/zh/docs/authentication/oauth2",
    "api_reference": "https://develop-docs.second.me/zh/docs/api-reference/secondme",
    "errors": "https://develop-docs.second.me/zh/docs/errors"
  },
  "prd": {}
}
```

#### 4.2 创建或更新 `CLAUDE.md`

在项目根目录创建 `CLAUDE.md`，内容如下：

```markdown
# SecondMe 集成项目

## 应用信息

- **App Name**: [app_name]
- **Client ID**: [client_id 部分隐藏]

## API 文档

开发时请参考官方文档（从 `.secondme/state.json` 的 `docs` 字段读取）：

| 文档 | 配置键 |
|------|--------|
| 快速入门 | `docs.quickstart` |
| OAuth2 认证 | `docs.oauth2` |
| API 参考 | `docs.api_reference` |
| 错误码 | `docs.errors` |

## 关键信息

- API 基础 URL: [从 state.json api.base_url 读取]
- OAuth 授权 URL: [从 state.json api.oauth_url 读取]
- Access Token 有效期: 2 小时
- Refresh Token 有效期: 30 天

> 所有 API 端点配置请参考 `.secondme/state.json` 中的 `api` 和 `docs` 字段

## 已选模块

[根据推断/选择动态生成模块列表]

## 权限列表 (Scopes)

根据 App Info 中的 Allowed Scopes：

| 权限 | 说明 | 状态 |
|------|------|------|
| `user.info` | 用户基础信息 | ✅ 已授权 |
| `user.info.shades` | 用户兴趣标签 | ✅ 已授权 |
| `chat` | 聊天功能 | ✅ 已授权 |
| `note.add` | 添加笔记 | ✅ 已授权 |
```

---

### 第五步：输出结果

显示成功信息和下一步操作：

```
✅ SecondMe 项目配置已完成！

应用名称: secondme-tinder
已保存配置到 .secondme/state.json
已创建/更新 CLAUDE.md（包含 SecondMe API 文档链接）

已选择模块（根据 Allowed Scopes 推断）:
- auth ✓
- profile ✓ (user.info.shades)
- chat ✓
- note ✓

⚠️  重要：请将 .secondme/ 添加到 .gitignore 以保护敏感信息

下一步：
- 运行 /secondme-prd 定义产品需求（推荐）
- 或运行 /secondme-nextjs --quick 快速生成项目
```

---

## 输出文件

| 文件 | 说明 |
|------|------|
| `.secondme/state.json` | 项目状态和配置 |
| `CLAUDE.md` | API 参考文档，供开发时查阅 |

---

## App Info 格式示例

以下是完整的 App Info 格式，用户可以直接从 SecondMe 开发者后台复制：

```
## App Info
- App Name: my-app
- Client ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
- Client Secret: your-secret-here
- Redirect URIs:
  - http://localhost:3000/api/auth/callback
  - https://my-app.vercel.app/api/auth/callback
  - https://my-app.com/api/auth/callback
- Allowed Scopes: user.info, user.info.shades, user.info.softmemory, chat, note.add
```

---

## 错误处理

- **App Info 格式无法解析**：提示格式错误，切换到手动输入模式
- **Client Secret 为占位符**：提示用户填写实际的 Secret
- **数据库连接串格式错误**：显示正确格式示例
- **权限不足无法创建目录**：提示检查目录权限

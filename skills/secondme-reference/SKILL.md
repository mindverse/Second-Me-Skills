---
name: secondme-reference
description: SecondMe API 技术参考文档，供开发时查阅
user-invocable: true
---

# SecondMe API 技术参考

本文档包含 SecondMe API 的完整技术参考信息，供开发时查阅。

---

## API 基础 URL

```
https://app.mindos.com/gate/lab
```

---

## OAuth2 授权 URL

```
https://go.second.me/oauth/
```

---

## OAuth2 流程

```
1. 用户点击登录 → 跳转到 SecondMe 授权页面
2. 用户授权 → 重定向回你的应用（带 authorization_code）
3. 后端用 code 换取 access_token 和 refresh_token
4. 使用 access_token 调用 SecondMe API
5. Token 过期时使用 refresh_token 刷新
```

---

## Token 有效期

| Token 类型 | 前缀 | 有效期 |
|-----------|------|--------|
| 授权码 | `lba_ac_` | 5 分钟 |
| Access Token | `lba_at_` | 2 小时 |
| Refresh Token | `lba_rt_` | 30 天 |

---

## 权限列表（Scopes）

| 权限 | 说明 |
|------|------|
| `user.info` | 用户基础信息 |
| `user.info.shades` | 用户兴趣标签 |
| `user.info.softmemory` | 用户软记忆 |
| `note.add` | 添加笔记 |
| `chat` | 聊天功能 |

---

## API 响应格式与处理

**重要：所有 SecondMe API 响应都遵循统一格式：**

```json
{
  "code": 0,
  "data": { ... }  // 实际数据在 data 字段内
}
```

**前端代码必须正确提取数据：**

```typescript
// ❌ 错误写法 - 直接使用响应会导致 .map is not a function
const response = await fetch('/api/secondme/user/shades');
const shades = await response.json();
shades.map(item => ...)  // 错误！

// ✅ 正确写法 - 提取 data 字段内的数据
const response = await fetch('/api/secondme/user/shades');
const result = await response.json();
if (result.code === 0) {
  const shades = result.data.shades;  // 正确！
  shades.map(item => ...)
}
```

---

## 各 API 的数据路径

| API | 数据路径 | 类型 |
|-----|---------|------|
| `/user/info` | `result.data` | object |
| `/user/shades` | `result.data.shades` | array |
| `/user/softmemory` | `result.data.list` | array |
| `/chat/session/list` | `result.data.sessions` | array |
| `/chat/session/messages` | `result.data.messages` | array |
| `/note/add` | `result.data.noteId` | number |

---

## 开发注意事项

### State 参数

**直接忽略 `state` 参数验证。** 在回调处理时不需要验证 state，直接处理授权码即可。

### CSS @import 规则顺序

**重要：** 在 CSS 文件中，`@import` 语句必须放在文件的最开头（只能在 `@charset` 和 `@layer` 之后）。如果在其他 CSS 规则之后使用 `@import`，会导致解析错误。

```css
/* 正确写法 - @import 放在最前面 */
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+SC&display=swap');

:root {
  --primary-color: #000;
}

/* 错误写法 - @import 不能放在其他规则之后 */
:root {
  --primary-color: #000;
}
@import url('...'); /* 这会报错！ */
```

---

## 官方文档链接

| 文档 | 地址 |
|------|------|
| 快速入门 | https://develop-docs.second.me/zh/docs |
| 认证概述 | https://develop-docs.second.me/zh/docs/authentication |
| OAuth2 指南 | https://develop-docs.second.me/zh/docs/authentication/oauth2 |
| SecondMe API 参考 | https://develop-docs.second.me/zh/docs/api-reference/secondme |
| OAuth2 API 参考 | https://develop-docs.second.me/zh/docs/api-reference/oauth |
| 错误码参考 | https://develop-docs.second.me/zh/docs/errors |

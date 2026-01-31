# SecondMe Skills

SecondMe API 集成开发工具集 - 包含 Next.js 全栈开发工作流和 API 技术参考。

## 包含的 Skills

### 1. `secondme-nextjs` - 主工作流

引导用户使用 Next.js 构建与 SecondMe API 集成的全栈项目。

**功能：**
- 完整的开发工作流程指导（5 个步骤）
- 前端设计规范和要求
- 技术栈说明
- OAuth2 认证流程集成

**调用方式：**
```
/secondme-nextjs
```

### 2. `secondme-reference` - 技术参考

SecondMe API 的完整技术参考文档，供开发时查阅。

**包含内容：**
- API 基础 URL
- OAuth2 授权 URL 和流程
- Token 有效期说明
- 权限列表（Scopes）
- API 响应格式与数据路径
- 开发注意事项

**调用方式：**
```
/secondme-reference
```

## 安装方式

### 1. 添加市场

```bash
/plugin marketplace add <your-github-username>/secondme-skills
```

### 2. 安装插件

```bash
/plugin install secondme-skills@<your-github-username>-secondme-skills
```

### 3. 使用 Skills

```bash
# 启动开发工作流
/secondme-nextjs

# 查看技术参考
/secondme-reference
```

## 目录结构

```
secondme-skills/
├── README.md                           # 本文档
├── .claude-plugin/
│   ├── plugin.json                     # 插件元信息
│   └── marketplace.json                # 市场配置
└── skills/
    ├── secondme-nextjs/
    │   └── SKILL.md                    # 主工作流 Skill
    └── secondme-reference/
        └── SKILL.md                    # 技术参考 Skill
```

## 相关链接

- [SecondMe 官方文档](https://develop-docs.second.me/zh/docs)
- [OAuth2 认证指南](https://develop-docs.second.me/zh/docs/authentication/oauth2)
- [API 参考](https://develop-docs.second.me/zh/docs/api-reference/secondme)

## 许可证

MIT

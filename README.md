# SecondMe Skills

SecondMe API 集成开发工具集 - 包含 Next.js 全栈开发工作流和 API 技术参考。

## 包含的 Skills

| Skill | 说明 |
|-------|------|
| `/secondme` | 一站式创建项目，依次执行初始化、需求定义、项目生成 |
| `/secondme-init` | 初始化项目配置和功能模块选择 |
| `/secondme-prd` | 通过对话定义产品需求 |
| `/secondme-nextjs` | 基于配置生成 Next.js 全栈项目 |
| `/secondme-reference` | SecondMe API 完整技术参考文档 |

## 安装方式

### 通过 skills.sh 安装（推荐）

```bash
npx skills add Mindverse/Second-Me-Skills
```

安装后即可直接使用：

```bash
/secondme              # 一站式创建项目
/secondme --quick      # 快速模式（跳过 PRD）
/secondme-init         # 仅初始化
/secondme-prd          # 仅需求定义
/secondme-nextjs       # 仅生成项目
/secondme-reference    # 查看 API 参考
```

## 目录结构

```
secondme-skills/
├── README.md                           # 本文档
└── skills/
    ├── secondme/
    │   └── SKILL.md                    # 一站式工作流
    ├── secondme-init/
    │   └── SKILL.md                    # 项目初始化
    ├── secondme-prd/
    │   └── SKILL.md                    # 需求定义
    ├── secondme-nextjs/
    │   └── SKILL.md                    # Next.js 项目生成
    └── secondme-reference/
        └── SKILL.md                    # API 技术参考
```

## 相关链接

- [SecondMe 官方文档](https://develop-docs.second.me/zh/docs)
- [OAuth2 认证指南](https://develop-docs.second.me/zh/docs/authentication/oauth2)
- [API 参考](https://develop-docs.second.me/zh/docs/api-reference/secondme)

## 许可证

MIT

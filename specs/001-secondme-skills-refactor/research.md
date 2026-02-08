# Research: SecondMe Skills 工作流重构

**Feature**: 001-secondme-skills-refactor
**Date**: 2026-02-05

## 研究目标

1. Claude Code Skills 最佳实践
2. 状态管理机制设计
3. 模块化 skill 组织方式
4. AskUserQuestion 工具使用模式

---

## 1. Claude Code Skills 最佳实践

### Decision: 采用标准 SKILL.md 格式

**Rationale**:
- 每个 skill 使用 YAML frontmatter 定义元数据
- Markdown 内容作为 skill 指令
- 保持 SKILL.md 文件在 500 行以内
- 支持文件放在同目录下按需加载

**Alternatives Considered**:
- 单文件包含所有 skills → 拒绝：违反职责分离原则
- 使用脚本生成 → 拒绝：增加复杂度，skills 本身是纯 markdown

### SKILL.md 标准结构

```yaml
---
name: skill-name
description: 技能描述，用于自动发现和触发
user-invocable: true
---

# Skill 标题

## 概述
[功能说明]

## 工作流程
[步骤说明]

## 输出
[预期结果]
```

---

## 2. 状态管理机制设计

### Decision: 使用 `.secondme/state.json` 单文件管理

**Rationale**:
- 所有状态集中在一个 JSON 文件中
- Skills 通过读取/写入此文件传递上下文
- 简单直接，无需复杂的状态机库

**Alternatives Considered**:
- 多个配置文件（config.json, prd.md 等）→ 拒绝：增加复杂度
- 环境变量 → 拒绝：不持久化，无法跨会话
- 数据库 → 拒绝：过度设计

### state.json 版本管理

```json
{
  "version": "1.0",
  "stage": "init",
  "modules": ["auth"],
  "config": {},
  "prd": {}
}
```

- `version` 字段用于未来兼容性迁移
- `stage` 值域：`init` | `prd` | `ready`
- 新字段可向后兼容添加

---

## 3. 模块化 Skill 组织方式

### Decision: 三个独立 skill + 一个参考文档

**Rationale**:
- `/secondme-init`: 配置收集 + 模块选择
- `/secondme-prd`: 对话式需求定义
- `/secondme-nextjs`: 项目生成（重构现有）
- `/secondme-reference`: API 参考（保留现有）

**Skill 间依赖关系**:

```
secondme-init (入口)
     ↓ writes state.json
secondme-prd (可选)
     ↓ updates state.json
secondme-nextjs (终点)
     ↓ reads state.json
```

**Alternatives Considered**:
- 单一 skill 处理所有流程 → 拒绝：职责过重，不灵活
- 更多细分 skill → 拒绝：增加认知负担

---

## 4. AskUserQuestion 工具使用模式

### Decision: 使用 AskUserQuestion 收集配置和选择

**Rationale**:
- Claude Code 提供 AskUserQuestion 工具用于交互式输入
- 支持多选（multiSelect: true）用于模块选择
- 提供选项和描述，改善用户体验

**使用示例**:

```markdown
在 skill 中使用 AskUserQuestion 工具收集信息：

1. 收集 OAuth 凭证
2. 收集数据库连接串
3. 展示模块列表让用户多选
```

---

## 5. 前置条件检查模式

### Decision: 每个 skill 开头检查 state.json

**Rationale**:
- 在 skill 指令开头声明前置条件
- Claude 读取 state.json 验证 stage
- 不满足时给出明确提示和建议

**检查逻辑**:

| Skill | 前置条件 | 失败提示 |
|-------|----------|----------|
| secondme-init | 无 | - |
| secondme-prd | state.json 存在且 stage >= init | 请先运行 /secondme-init |
| secondme-nextjs | stage >= prd 或 --quick | 请先运行 /secondme-prd 或使用 --quick |

---

## 6. 快速模式设计

### Decision: 通过 skill 参数 `--quick` 实现

**Rationale**:
- 开发者可以在 skill 名后加参数：`/secondme-nextjs --quick`
- Skill 指令中检查 `$ARGUMENTS` 是否包含 `--quick`
- 快速模式跳过 PRD 检查，使用默认配置

**实现方式**:

```markdown
## 前置检查

检查参数是否包含 --quick：
- 如果是：跳过 PRD 检查
- 如果否：验证 stage >= prd
```

---

## 7. 敏感信息处理

### Decision: 提醒用户将 .secondme/ 加入 .gitignore

**Rationale**:
- state.json 包含 OAuth 凭证
- init 完成后主动提醒
- 自动检查 .gitignore 是否已包含

**实现方式**:
- init skill 完成后检查 .gitignore
- 如果不包含 .secondme/，输出警告并建议添加

---

## 总结

| 决策点 | 选择 | 理由 |
|--------|------|------|
| Skill 格式 | 标准 SKILL.md | 符合 Claude Code 规范 |
| 状态管理 | 单一 state.json | 简单直接 |
| Skill 数量 | 3 个核心 + 1 个参考 | 职责分离，不过度拆分 |
| 用户交互 | AskUserQuestion | 原生工具支持 |
| 前置检查 | skill 开头验证 | 明确的错误提示 |
| 快速模式 | --quick 参数 | 灵活跳过 PRD |
| 敏感信息 | 提醒 .gitignore | 安全最佳实践 |

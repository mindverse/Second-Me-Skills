# Implementation Plan: SecondMe Skills 工作流重构

**Branch**: `001-secondme-skills-refactor` | **Date**: 2026-02-05 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-secondme-skills-refactor/spec.md`

## Summary

重构 SecondMe Skills 工作流，将现有的单一 `secondme-nextjs` skill 拆分为模块化的三阶段工作流（init → prd → nextjs），引入状态管理机制（state.json）和功能模块选择（auth/profile/chat/note），支持快速模式跳过 PRD 阶段。

## Technical Context

**Language/Version**: Markdown (Claude Code Skills format)
**Primary Dependencies**: Claude Code Skills framework, AskUserQuestion tool
**Storage**: JSON 文件 (`.secondme/state.json`)
**Testing**: 手动测试 skill 工作流
**Target Platform**: Claude Code CLI
**Project Type**: Skills 库（非传统代码项目）
**Performance Goals**: 开发者 2 分钟完成 init，5 轮对话完成 PRD
**Constraints**: Skills 文件需符合 Claude Code 规范（frontmatter + markdown）
**Scale/Scope**: 3 个核心 skills（init、prd、nextjs）

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

当前项目未定义具体的 constitution 原则（模板为空）。基于通用最佳实践：

- [x] **简洁性**: Skills 设计保持简洁，每个 skill 职责单一
- [x] **可测试性**: 每个 skill 有明确的输入输出，可独立验证
- [x] **渐进式披露**: 核心内容在 SKILL.md，详细参考可放在支持文件
- [x] **用户体验**: 提供快速模式，不强制完整流程

## Project Structure

### Documentation (this feature)

```text
specs/001-secondme-skills-refactor/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output (skill 接口定义)
└── tasks.md             # Phase 2 output (/speckit.tasks)
```

### Source Code (repository root)

```text
skills/
├── secondme-init/
│   └── SKILL.md          # 初始化配置 skill
├── secondme-prd/
│   └── SKILL.md          # PRD 对话 skill
└── secondme-nextjs/
    └── SKILL.md          # 项目生成 skill（重构现有）
```

**Structure Decision**: 采用 skills 库结构，每个 skill 一个目录，包含 SKILL.md 主文件。状态文件 `.secondme/state.json` 由 skills 在运行时在用户项目中创建。移除独立的 `secondme-reference` skill，API 参考文档地址在 init 阶段写入项目的 `CLAUDE.md`。

## Complexity Tracking

无违规需要记录。设计遵循简洁原则。

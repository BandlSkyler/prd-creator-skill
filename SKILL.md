---
version: 1.0.0
name: prd-tdd-creator
description: AI驱动的产品与技术设计文档生成技能。4阶段流程：需求定义→架构拆分→功能规格（prd+tdd）→自检验证。面向程序员+AI协作场景，确保每个功能文档自包含、AI可独立执行。触发词："create a PRD", "写需求文档", "技术设计", "接口定义", "数据库设计", "功能规格"。
---

# PRD-TDD Creator - AI驱动文档生成 (v4.0)

面向程序员+AI协作的4阶段文档生成流程，产出可直接驱动代码生成的技术规格文档。

## 阶段总览

| 阶段 | 名称 | 核心动作 | 产出物 |
|------|------|----------|--------|
| 1 | 需求定义 | 澄清需求 + 确认技术栈 | 需求文档 + 技术栈清单 |
| 2 | 架构拆分 | 项目结构 + 模块依赖矩阵 | `P001/` 目录 + README + M文档 |
| 3 | 功能规格 | 每个F目录产出 prd.md + tdd.md | 完整技术规格文档 |
| 4 | 自检验证 | AI执行检查清单 | 问题清单 + 修正 |

---

## Phase 1: 需求定义

### 目标
一次性完成需求澄清和技术栈确认。

### 必做步骤
1. **技术栈确认** - 收集后端/前端/数据库/缓存/MQ/部署方式
2. **需求澄清** - 用 5W2H 框架多轮对话（Who/What/Why/When/Where/How/How Much）
3. **功能优先级** - Must/Should/Could 三级划分
4. **范围界定** - 明确 In Scope / Out of Scope

### 产出
使用 `assets/requirement-template.md` 生成需求文档。

---

## Phase 2: 架构拆分

### 目标
建立项目目录结构，完成模块划分和依赖分析。

### 拆分原则

| 层级 | 拆分依据 | 粒度 |
|------|----------|------|
| 项目 (P) | 独立部署、独立业务域 | 3-8个模块 |
| 模块 (M) | 单一职责、高内聚 | 3-8个功能 |
| 功能 (F) | 独立交付、可测试 | 一个用户场景 |

### 目录结构
```
prd/P{三位编号}-{项目名}/
├── README.md                    # 项目概述 + 技术栈 + M依赖矩阵
├── M001-{模块名}.md             # 模块文档（含F依赖关系）
├── M001/
│   └── F001-{功能名}/
│       ├── prd.md               # 业务需求（做什么）
│       └── tdd.md               # 技术规格（怎么做：DB+API+测试）
└── M002-{模块名}.md
```

### 路径规则

| 层级 | 格式 | 示例 |
|------|------|------|
| 项目 | `prd/P{三位}-{名}/` | `prd/P001-用户中心/` |
| 模块文档 | `Pxxx/M{三位}-{名}.md` | `P001/M001-用户认证.md` |
| 功能目录 | `Mxxx/F{三位}-{名}/` | `M001/F001-注册登录/` |
| 功能文档 | `Fxxx/{prd,tdd}.md` | `F001-注册登录/tdd.md` |

### 产出
- 项目 README（使用 Mermaid 画 M 依赖矩阵）
- 每个模块文档（使用 `assets/module-template.md`，含 F 依赖关系图）

### 模块依赖矩阵（放入 README.md）

| 模块 | 被谁依赖 | 依赖谁 | 开发顺序 |
|------|---------|--------|---------|
| M001-用户认证 | M002,M003 | 无 | 第1 |
| M002-账户管理 | M003 | M001 | 第2 |

---

## Phase 3: 功能规格

### 目标
为每个功能生成完整的技术规格文档，确保 AI 可直接基于文档生成代码。

### F目录文档

| 文件 | 必须 | 内容 | 模板 |
|------|------|------|------|
| prd.md | 是 | 业务需求、流程、规则、验收标准 | `assets/feature-template.md` |
| tdd.md | 是 | DB设计 + API定义 + 技术方案 + 测试用例 | `assets/tdd-template.md` |
| ui.md | 按需 | 有用户界面时生成 | `assets/ui-template.md` |

> **tdd.md 是技术规格的单一真相源**，包含数据库设计、接口定义、错误码、性能设计、测试用例，避免跨文件不一致。

### 建议生成顺序
```
prd.md（业务需求）→ tdd.md（技术规格：DB→API→测试）
```

### 文档间引用规范
在 `prd.md` 中：
```markdown
技术规格见 [tdd.md](./tdd.md)。
```

跨功能引用：
```markdown
依赖 [F001-注册/prd.md](../F001-注册/prd.md) 的用户注册能力。
```

---

## Phase 4: 自检验证

### 目标
AI 对全套文档执行检查清单，发现遗漏后直接修正。

### 使用 `assets/review-checklist.md` 检查以下维度：

1. **完整性** - 每个F目录有 prd.md + tdd.md
2. **一致性** - prd 校验规则 ↔ tdd 中 API 字段校验 ↔ DB 字段定义
3. **可执行性** - 验收标准可测试、DDL可执行、接口定义无歧义
4. **依赖闭环** - F/M依赖关系完整、无循环依赖

### 检查通过标准
- 高风险问题：0个
- 中风险问题：已有处理方案
- tdd.md 中 DDL 可直接执行
- tdd.md 中接口定义无歧义
- tdd.md 中测试用例可转化为代码

---

## 从文档到代码

当文档套件准备好后，按以下顺序生成代码：

```
1. tdd.md (DB部分)  → 数据库 Migration 脚本
2. tdd.md (DB部分)  → 数据模型 (Model/Entity)
3. tdd.md (DB部分)  → Repository/DAO 层
4. tdd.md (API部分) → Controller/Handler 层
5. prd.md           → Service 层（业务规则）
6. tdd.md (测试部分) → 单元测试 + 集成测试
```

### 开发完成后验证
使用 `assets/dev-checklist.md` 逐项验证代码是否对齐文档规格：
- DB Migration ↔ tdd.md DDL
- API 实现 ↔ tdd.md 接口定义
- 业务逻辑 ↔ prd.md 规则
- 错误码 ↔ tdd.md 错误码表
- 测试用例 ↔ tdd.md 测试定义 + prd.md 验收标准

---

## 模板清单

| 模板 | 用途 | 阶段 |
|------|------|------|
| `assets/requirement-template.md` | 需求文档 | Phase 1 |
| `assets/module-template.md` | 模块文档 | Phase 2 |
| `assets/feature-template.md` | 功能 prd.md | Phase 3 |
| `assets/tdd-template.md` | 技术规格（DB+API+测试） | Phase 3 |
| `assets/ui-template.md` | UI设计（可选） | Phase 3 |
| `assets/review-checklist.md` | 文档自检清单 | Phase 4 |
| `assets/dev-checklist.md` | 开发完成验证清单 | 代码完成后 |

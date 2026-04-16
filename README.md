# AI Automation Engineer Framework

> A self-learning, continuously growing AI industrial automation engineer system built on Claude Code.  
> 具备自我学习能力、持续成长的工业自动化 AI 工程师框架，基于 Claude Code 构建。

---

## Overview 概述

This framework implements a **multi-agent industrial automation engineering team** where each AI agent specializes in a specific domain (PLC programming, robot programming, vision systems, etc.) and continuously learns from every task it executes.

本框架实现了一个**多 Agent 工业自动化工程师团队**，每个 AI Agent 专注于特定领域（PLC 编程、机器人编程、视觉系统等），并在每次任务执行中持续学习成长。

### Core Capabilities 核心能力

| # | Capability | Description |
|---|-----------|-------------|
| 1 | **Technical Q&A** | Answer engineering questions with manual references |
| 2 | **Robot Code Generation** | Generate DENSO PacScript, KUKA KRL, FANUC TP, ABB RAPID |
| 3 | **PLC Logic Generation** | Ladder Logic, Structured Text, FBD with safety interlocks |
| 4 | **Code Explanation** | Line-by-line explanation of robot and PLC programs |
| 5 | **Fault Diagnosis** | Root-cause analysis of alarms and errors |
| 6 | **Documentation** | Auto-generate technical documents in Markdown/PDF |
| 7 | **System Integration** | Multi-device communication architecture design |

---

## Architecture 系统架构

```
Multi-Agent Team 多Agent团队
│
├── Chief Engineer Agent（总工程师）
│   ├── Reads each specialist's CAPABILITY_CARD.md
│   ├── Decomposes project requirements into tasks
│   ├── Coordinates specialists and resolves conflicts
│   └── Consolidates outputs into complete engineering packages
│
├── Specialist Agent — PLC Programming
├── Specialist Agent — Robot Programming  
├── Specialist Agent — Vision Systems
└── Specialist Agent — System Integration
```

Each specialist agent is built on this framework and grows independently through:

- **Reflexion** (Shinn et al., NeurIPS 2023) — verbal self-critique after every task
- **Voyager** (Wang et al., 2023) — automatic skill promotion after 3 successful uses  
- **Karpathy LLM Wiki** (2026) — LLM-authored knowledge base replacing RAG
- **CoALA** (Sumers et al., 2023) — 4-layer memory architecture

---

## Repository Structure 仓库结构

```
ai-automation-engineer/
│
├── specialist_agent/              ← 专家 Agent 框架（可复制部署多个）
│   └── .claude/
│       ├── AGENTS.md              ← 行为总纲（最高优先级）
│       │
│       ├── memory/                ← 热记忆（每次任务必读）
│       │   ├── core/CORE.md       ← 身份状态·成熟度·约束
│       │   └── episodic/recent.md ← 最近5次任务摘要
│       │
│       ├── wiki/                  ← LLM编写的知识库（Karpathy模式）
│       │   └── index.md           ← 知识地图·所有页面索引
│       │
│       ├── agent_ref/             ← 官方手册（只读，厂商原文）
│       │   ├── controllers/       ← 控制器指令手册
│       │   ├── motion/            ← 运动控制手册
│       │   ├── vision/            ← 视觉系统手册
│       │   ├── io/                ← I/O 系统手册
│       │   ├── communication/     ← 通讯协议手册
│       │   ├── safety/            ← 安全规范
│       │   └── hmi/               ← HMI/TP 界面手册
│       │
│       ├── knowledge/             ← 项目经验库（动态积累）
│       │   ├── CLAUDE.md          ← 使用指南·写入格式
│       │   ├── principles/        ← 工程底层原理
│       │   ├── patterns/          ← 实战判断规则（PAT-NNN）
│       │   ├── mistakes/          ← 错误案例库（ERR-NNN，只增不删）
│       │   ├── episodic/          ← 任务情景记录（SESSION-NNN）
│       │   ├── domain_map/        ← 设备注册·成熟度追踪
│       │   ├── open_questions/    ← 待解问题（驱动成长）
│       │   └── lint/              ← 知识库质量问题记录
│       │
│       ├── skills/                ← 程序记忆（任务执行规范）
│       │   ├── qa/SKILL.md
│       │   ├── codegen/SKILL.md
│       │   ├── plc/SKILL.md
│       │   ├── explain/SKILL.md
│       │   ├── diagnosis/SKILL.md
│       │   ├── documentation/SKILL.md
│       │   ├── integration/SKILL.md
│       │   ├── reflection/SKILL.md     ← Reflexion 自我批判
│       │   └── skill_promotion/SKILL.md ← Voyager 技能提升
│       │
│       └── collab/                ← 协作接口层（Multi-Agent）
│           ├── capability_card/CAPABILITY_CARD.md  ← 能力名片
│           ├── task_interface/TASK_PROTOCOL.md     ← 任务协议
│           └── shared_protocol/SHARED_STANDARDS.md ← 共享标准
│
└── chief_engineer/                ← 总工程师框架
    └── .claude/
        ├── AGENTS.md              ← 总工程师行为规范
        ├── team/roster.md         ← 专家团队注册表
        └── project/               ← 项目管理
            └── active/PROJ-TEMPLATE/project_brief.md
```

---

## Quick Start 快速开始

### Step 1: Deploy a Specialist Agent 部署专家 Agent

```bash
# 复制专家Agent框架到你的项目
cp -r specialist_agent/.claude /your-project/.claude

# 配置身份（编辑两个文件）
# 1. collab/capability_card/CAPABILITY_CARD.md
#    填写: agent_id, primary_domain, supported_platforms
# 2. memory/core/CORE.md  
#    填写: agent_id, team_id, chief_engineer_id
```

### Step 2: Add Device Manuals 添加设备手册

将厂商手册（Markdown 格式）放入对应目录：

```bash
# 控制器手册
agent_ref/controllers/DENSO_RC8_PacScript.md
agent_ref/controllers/Siemens_S71200_TIA.md
agent_ref/controllers/KUKA_KRC4_KRL.md

# 视觉系统手册
agent_ref/vision/Keyence_CVX_Manual.md

# 通讯协议手册
agent_ref/communication/EtherNetIP_Guide.md
```

手册命名规范：`[厂商]_[型号]_[内容].md`

在 `agent_ref/README.md` 的已加载文件表格中登记。

### Step 3: Configure CAPABILITY_CARD 配置能力名片

```yaml
# collab/capability_card/CAPABILITY_CARD.md

agent_id: "robot-specialist-001"
agent_role: "specialist"
domain: "robot-programming"

primary_domain: "robot-programming"

supported_platforms:
  - name: "DENSO RC8"
    ref_file: "agent_ref/controllers/DENSO_RC8_PacScript.md"
    maturity: "L1"
  - name: "KUKA KR C4"
    ref_file: "agent_ref/controllers/KUKA_KRC4_KRL.md"
    maturity: "L1"
```

### Step 4: Start Working 开始工作

在 Claude Code 中，Agent 会自动：
- 读取 `memory/core/CORE.md` 了解自身状态
- 读取 `memory/episodic/recent.md` 回顾最近教训
- 按任务类型加载对应 `skills/SKILL.md`
- 执行任务并产出带 `[REF]` 标注的专业输出
- 完成后执行 Reflexion 自我批判并写入知识库

---

## The Learning Loop 学习循环

```
Task Input
    │
    ▼
Phase 0: Load Memory
    memory/core/CORE.md + memory/episodic/recent.md
    + wiki/ + knowledge/patterns/ + knowledge/mistakes/
    │
    ▼
Phase 1: Pre-Task Hypothesis
    声明对任务的理解、预期难点、将查阅的资料
    │
    ▼
Phase 2: Execute with Decision Logging
    每个技术决策附：原因 + 排除方案 + 失效条件 + REF标注
    │
    ▼
Phase 3: Reflexion (mandatory)
    假设vs结果对比 → 质量自评 → 根因批判 → 萃取决策
    │
    ▼
Phase 4: Write to Knowledge Base
    mistakes/log.md | patterns/ | wiki/ | open_questions/
    + Update memory/episodic/recent.md
    + Update memory/core/CORE.md statistics
    │
    ▼
    Agent grows ↑
```

**Voyager Trigger:** Same solution used 3 times successfully → draft new SKILL.md → human review → promote to skill library

**Wiki Lint:** Every 10 tasks → check for contradictions, orphan pages, stale content

---

## Multi-Agent Collaboration 多 Agent 协作

### Signal Naming Convention 信号命名规范

All agents use the same naming standard:

```
[DEVICE_PREFIX]_[DIRECTION]_[FUNCTION]_[NUMBER]

Prefixes: ROB(robot) PLC(plc) VIS(vision) CNV(conveyor) ACT(actuator) SEN(sensor)
Direction: CMD(command) STS(status) DAT(data)

Examples:
  ROB_CMD_GO_HOME       PLC→Robot: go to home position
  ROB_STS_AT_HOME       Robot→PLC: at home position confirmed
  VIS_DAT_POS_X         Vision→Robot: X-axis offset data
  ACT_CMD_GRIPPER_CLOSE Any→Gripper: close command
```

### 4-Step Handshake Protocol 四步握手协议

```
Step 1: Master sends command      [SIGNAL]_CMD = 1
Step 2: Slave acknowledges        [SIGNAL]_ACK = 1
Step 3: Slave done/error          [SIGNAL]_DONE = 1 or [SIGNAL]_ERR = 1
Step 4: Master clears command     [SIGNAL]_CMD = 0

Timeout Step1→2: 500ms (communication timeout)
Timeout Step2→3: task-specific (action timeout)
```

### Interface Placeholders 接口占位符

When waiting for another agent's output:

```
{{INTERFACE:plc-specialist:PLC_CMD_START_CYCLE:PLC启动周期信号地址}}
{{INTERFACE:vision-specialist:VIS_DAT_POS_X:视觉X坐标寄存器地址}}
```

Chief Engineer fills these in after coordinating with relevant specialists.

---

## Maturity Levels 成熟度等级

| Level | Memory State | Behavior |
|-------|-------------|----------|
| **L1** Beginner | wiki empty, no patterns | Relies entirely on agent_ref; many low-confidence outputs |
| **L2** Intermediate | wiki started, patterns accumulating | Common tasks accurate; occasional corrections needed |
| **L3** Advanced | wiki cross-referenced, rich skill library | Proactively identifies problem types; catches edge cases |
| **L4** Expert | Lint passing, Voyager skills promoting | Finds problems user didn't raise; system-level recommendations |

---

## Confidence Annotation System 置信度标注系统

| Annotation | Meaning | Trust Level |
|-----------|---------|-------------|
| `[REF: file §section p.XX]` | Official manual citation | Highest — directly verifiable |
| `[WIKI: wiki/page]` | Knowledge base reference | High — LLM-synthesized |
| `[PAT: PAT-NNN]` | Pattern library reference | High — validated rule |
| `[高置信]` | Strong engineering basis | High |
| `[中置信]` | Reasonable inference | Medium — flag for review |
| `[低置信]` | Needs verification | Low — **must not be stated as fact** |
| `[INTERFACE]` | Interface definition produced | Extracted by Chief Engineer |
| `[SAFETY]` | Safety-critical item | **Mandatory human review** |

---

## Design Principles 设计原则

This framework synthesizes the following research:

| Source | Contribution |
|--------|-------------|
| **CoALA** (Sumers 2023) | 4-layer memory: working / episodic / semantic / procedural |
| **Reflexion** (Shinn, NeurIPS 2023) | Verbal self-critique as language-based reinforcement |
| **Voyager** (Wang 2023) | Automatic skill library growth from successful solutions |
| **Karpathy LLM Wiki** (2026) | Markdown knowledge base outperforms RAG at team scale |
| **Anthropic Building Effective Agents** (2024) | Simplicity first; avoid over-engineering |

---

## Key Rules 关键规则

```
✓ Technical Q&A without [REF] citation = invalid output, do not send
✓ [低置信] content must never be stated as fact
✓ Safety interlocks (E-STOP, door switches) are never optional
✓ Code generation requires confirmed controller model + agent_ref lookup
✓ agent_ref/ content never copied into wiki/ (source ≠ synthesized knowledge)
✓ Reflexion is mandatory after every non-trivial task
✓ mistakes/log.md entries are never deleted
```

---

## File Naming Conventions 文件命名规范

```
Agent reference manuals:  [Vendor]_[Model]_[Content].md
Pattern rules:            PAT-NNN (3-digit number)
Error cases:              ERR-NNN
Session logs:             SESSION-NNN
Open questions:           OQ-NNN
Wiki lint issues:         LINT-NNN
Project IDs:              PROJ-NNN
Task IDs:                 TASK-NNN
```

---

## Contributing 贡献指南

This is a living framework. The most valuable contributions are:

1. **Device manuals** — Add `agent_ref/` content for new controllers, vision systems, or protocols
2. **Pattern rules** — Share validated `PAT-NNN` rules from your project experience  
3. **Error cases** — Contribute `ERR-NNN` entries with root-cause analysis
4. **New SKILL.md** — Document task execution patterns for new capability domains

Format your contributions following the templates in `knowledge/CLAUDE.md`.

---

## License

MIT License — Use freely, contribute back.

---

*Built with Claude Code · Inspired by CoALA, Reflexion, Voyager, and Karpathy's LLM Wiki*

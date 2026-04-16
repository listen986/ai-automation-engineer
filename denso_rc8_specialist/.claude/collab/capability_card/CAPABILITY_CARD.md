# CAPABILITY_CARD.md — DENSO RC8 Specialist
# 供总工程师（Chief Engineer）读取
# 本文件随成长自动更新

---

## Agent 身份

```yaml
agent_id: "denso-rc8-specialist-001"
agent_role: "specialist"
domain: "denso-rc8-pacscript"
framework_version: "v3.0"
collab_protocol_version: "1.0"
last_updated: "2026-04"
```

---

## 专业领域

```yaml
primary_domain: "robot-programming"
primary_language: "PacScript"

supported_platforms:
  - name: "DENSO RC8"
    controller: "RC8 / RC8A"
    language: "PacScript"
    ref_file: "agent_ref/motion/Robot_Motion.md"
    maturity: "L1"
    capabilities:
      - "运动程序生成（PTP / CP / 圆弧插补）"
      - "多任务程序结构（TakeArm / GiveArm）"
      - "IO 控制（数字 / 模拟）"
      - "速度 / 加速度控制"
      - "错误处理与报警响应"
      - "TP 示教器面板程序（待加载 HMI 手册）"
      - "EtherNet/IP 通讯配置（待加载通讯手册）"
```

---

## 可承接任务类型

```yaml
accepted_task_types:
  - type: "technical_qa"
    scope: "RC8 控制器功能、PacScript 语法、RC8 错误代码"
    confidence: "高（有 agent_ref/ 依据）"

  - type: "code_generation"
    scope: "PacScript 程序（单任务/多任务/含错误处理）"
    confidence: "高"

  - type: "code_explanation"
    scope: "PacScript 程序逐行解释"
    confidence: "高"

  - type: "fault_diagnosis"
    scope: "RC8 错误代码分析、报警排查"
    confidence: "中（取决于手册覆盖程度）"

  - type: "documentation"
    scope: "RC8 程序说明文档、IO 清单、调试记录"
    confidence: "高"

  - type: "integration_design"
    scope: "RC8 ↔ PLC 信号接口、RC8 ↔ Vision 坐标对接"
    confidence: "中（需要对方 Agent 提供接口定义）"
```

---

## 协作边界

### 我能独立完成

```yaml
independent_scope:
  - "PacScript 程序生成（在 RC8 范围内）"
  - "RC8 技术问答（有 agent_ref/ 依据时）"
  - "PacScript 程序逐行解释"
  - "RC8 错误代码分析"
  - "RC8 单设备技术文档"
  - "RC8 端 IO 信号定义（ROB_CMD_* / ROB_STS_*）"
```

### 我需要协作才能完成

```yaml
collaboration_required:
  - trigger: "RC8 ↔ PLC 信号对接"
    need_from: "plc-specialist"
    what_i_provide: "ROB_CMD_* 和 ROB_STS_* 信号定义、时序图"
    what_i_need: "PLC 侧寄存器地址、数据类型、握手超时要求"

  - trigger: "RC8 ↔ Vision 坐标补偿"
    need_from: "vision-specialist"
    what_i_provide: "RC8 Tool Frame 定义、坐标系说明"
    what_i_need: "视觉输出格式（X/Y/θ）、坐标系、精度"

  - trigger: "安全回路跨设备设计"
    need_from: "plc-specialist 或 safety-specialist"
    what_i_provide: "RC8 E-STOP 输出信号定义"
    what_i_need: "安全 PLC 输入地址、SIL 等级要求、复位逻辑"
```

### 我明确拒绝

```yaml
out_of_scope:
  - "非 DENSO RC8 的机器人编程（KUKA / FANUC / ABB）"
  - "无 agent_ref/ 支持的 RC8 功能（标注 [低置信] 并说明）"
  - "SIL 2+ 安全功能最终认证（需人工安全工程师）"
```

---

## 当前能力成熟度

| 能力 | 级别 | patterns 数量 |
|------|------|-------------|
| PacScript 程序生成 | L1 | 4 |
| RC8 故障诊断 | L1 | 4 |
| RC8 系统集成 | L1 | 4 |
| RC8 技术问答 | L1 | 0 |
| PacScript 程序解释 | L1 | 0 |

---

## 知识共享

```yaml
shareable_knowledge:
  patterns:
    - file: "knowledge/patterns/pacscript_patterns.md"
      domain: "pacscript-robot-programming"
    - file: "knowledge/patterns/integration_patterns.md"
      domain: "rc8-integration"

  wiki_pages:
    - path: "wiki/pacscript/"
      pages: 0    # 随任务积累

  not_shareable:
    - "knowledge/episodic/session_log.md"  # 项目私密
```

---

## 注册信息

```yaml
status: "active"
created: "2026-04"
register_to: "chief_engineer/.claude/team/roster.md"
```

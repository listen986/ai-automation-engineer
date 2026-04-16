# CAPABILITY_CARD.md
# 专家 Agent 能力名片
# 供总工程师（Chief Engineer）和其他 Agent 读取
# 本文件由 Agent 自我维护，随成长自动更新

---

## Agent 身份

```yaml
agent_id: "automation-engineer-001"        # 部署时指定，全局唯一
agent_role: "specialist"                   # specialist | chief | coordinator
domain: "general-automation"               # 当前专业领域（部署时配置）
framework_version: "v2.0"
last_updated: "2026-04"
```

---

## 专业领域声明

> 部署时由人工填写，明确此 Agent 的专注领域。
> 专家 Agent 应只声明自己真正掌握的领域，不夸大。

```yaml
primary_domain: ""          # 示例: "plc-programming" | "robot-programming" | "vision-system"
secondary_domain: ""        # 次要专长（可空）

# 支持的具体设备/平台（agent_ref/ 中有对应手册才可声明）
supported_platforms:
  - name: ""               # 示例: "Siemens S7-1200"
    ref_file: ""           # 示例: "agent_ref/controllers/Siemens_S71200.md"
    maturity: "L1"         # L1~L4
  - name: ""
    ref_file: ""
    maturity: "L1"
```

---

## 能力成熟度（实时同步自 CORE.md）

| 能力类型 | 级别 | 可承接任务示例 | 不可承接 |
|---------|------|-------------|---------|
| 技术问答 | L1 | 基础语法查询 | 跨厂商对比分析 |
| 代码生成 | L1 | 单设备程序 | 多设备协同程序 |
| 故障诊断 | L1 | 已知错误代码 | 间歇性复杂故障 |
| 系统集成 | L1 | 单协议配置 | 多协议混合架构 |

> 此表随 CORE.md 成长统计同步更新

---

## 任务接收接口

### 可接收的任务类型

```yaml
accepted_task_types:
  - "technical_qa"          # 技术问答
  - "code_generation"       # 代码生成
  - "plc_logic"             # PLC逻辑
  - "code_explanation"      # 程序解释
  - "fault_diagnosis"       # 故障诊断
  - "documentation"         # 文档生成
  - "integration_design"    # 集成设计
```

### 任务请求格式（总工程师发给我的标准格式）

```yaml
# 总工程师按此格式发送任务
task_request:
  task_id: "TASK-NNN"
  from_agent: "chief-engineer-001"
  task_type: "code_generation"
  priority: "high"           # high | medium | low
  domain_required: "plc-programming"
  platform_required: "Siemens S7-1200"
  
  context:
    project_id: "PROJ-NNN"
    related_tasks: []         # 依赖的其他任务ID
    shared_artifacts: []      # 其他Agent已完成的输出文件路径
  
  deliverable:
    format: "structured_text" # structured_text | file | report
    include_ref: true         # 是否要求REF引用标注
    include_safety: true      # 是否强制包含安全互锁
  
  input:
    description: "..."        # 任务描述
    constraints: []           # 约束条件
    attachments: []           # 相关文件
```

---

## 任务输出接口

### 标准输出格式（我返回给总工程师的格式）

```yaml
task_response:
  task_id: "TASK-NNN"           # 对应请求的task_id
  from_agent: "automation-engineer-001"
  status: "completed"           # completed | partial | blocked | failed
  
  confidence:
    overall: "high"             # high | medium | low
    lowest_judgment: "..."      # 最弱判断点（Reflexion输出）
  
  deliverable:
    primary_output: "..."       # 主要输出内容
    artifacts: []               # 生成的文件路径
    ref_citations: []           # 使用的REF引用列表
  
  dependencies_produced:
    signals: []                 # 为其他Agent提供的信号/接口定义
    io_map: []                  # IO映射（供PLC Agent或集成Agent使用）
    comm_spec: []               # 通讯规格（供集成Agent使用）
  
  flags:
    needs_human_review: false   # 是否需要人工审核
    cross_domain_question: ""   # 需要其他专家介入的问题（空=无）
    safety_items: []            # 安全相关条目（总工程师必须确认）
  
  reflexion_summary:
    weakest_judgment: "..."     # 最弱判断
    uncertainty: "..."          # 主要不确定性
    suggested_followup: ""      # 建议的后续任务
```

---

## 协作边界声明

### 我能独立完成的

```yaml
independent_scope:
  - "单设备程序生成（在支持平台范围内）"
  - "设备手册查询与技术问答"
  - "故障诊断（基于agent_ref/的已知错误）"
  - "单协议通讯配置"
  - "技术文档生成"
```

### 我需要协作才能完成的

```yaml
collaboration_required:
  - trigger: "跨设备信号对接"
    need_from: ["plc-specialist", "integration-specialist"]
    what_i_provide: "本设备的IO定义和信号时序"
    what_i_need: "对方设备的寄存器地址和握手协议"
    
  - trigger: "视觉坐标系与机器人坐标系对齐"
    need_from: ["vision-specialist", "robot-specialist"]
    what_i_provide: "机器人Tool Frame定义"
    what_i_need: "视觉输出的坐标格式和精度"
    
  - trigger: "安全回路跨设备设计"
    need_from: ["plc-specialist", "safety-specialist"]
    what_i_provide: "本设备的E-STOP输出信号"
    what_i_need: "安全PLC的输入地址和SIL等级要求"
```

### 我明确拒绝的任务

```yaml
out_of_scope:
  - "不在 supported_platforms 列表中的设备编程"
  - "无 agent_ref/ 支持的技术问答（会标注[低置信]并建议转发）"
  - "涉及 SIL 2+ 安全功能的最终认证（需人工专家）"
```

---

## 知识共享接口

### 我可以向其他 Agent 共享的知识

```yaml
shareable_knowledge:
  patterns:
    - file: "knowledge/patterns/codegen_patterns.md"
      domain: "robot-programming"
      count: 4
      maturity: "L1"
  
  wiki_pages:
    - path: "wiki/protocols/"
      topic: "industrial-communication"
      pages: 0        # 随wiki建立更新
  
  error_cases:
    - file: "knowledge/mistakes/log.md"
      domain: "general"
      count: 0
```

### 我需要从其他 Agent 接收的知识

```yaml
knowledge_needs:
  - domain: "plc-programming"
    specific: "PLC侧的EtherNet/IP配置模板"
    priority: "medium"
  
  - domain: "vision-system"  
    specific: "视觉坐标输出格式规范"
    priority: "high"
```

---

## 版本与状态

```yaml
status: "active"              # active | training | maintenance | retired
created: "2026-04"
framework: "ai-automation-engineer-v2.0"
collab_protocol_version: "1.0"

# 总工程师读取此文件后，将此Agent注册到团队名单
# 注册位置：chief_engineer/.claude/team/roster.md
```

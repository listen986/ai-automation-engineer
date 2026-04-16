# SKILL: System Integration — 多设备系统集成
# Task 7

---

## 触发条件
- "Robot + Vision + PLC 集成" / "如何连接…" / "通讯架构设计"
- 涉及多设备协同、通讯协议选型、接口定义

---

## 执行前准备

### Step 1：收集系统信息（不足时主动询问）
```
□ 设备清单（品牌、型号、数量）
□ 功能需求（做什么）
□ 实时性要求（响应时间）
□ 现有网络环境（已有设备的协议约束）
□ 安全要求（SIL等级）
□ 预算/标准约束（客户指定协议？）
```

### Step 2：读取经验
读取 knowledge/patterns/integration_patterns.md（PAT-030~033）。

---

## 输出顺序（强制：架构先于细节）

```
Step 1 → 系统架构图（设备 + 连接关系）
Step 2 → 通讯架构（协议选型 + 数据流）
Step 3 → Master/Slave 定义
Step 4 → 接口定义表（信号清单）
Step 5 → 握手协议设计
Step 6 → 程序实现框架（如需要）
```

---

## Step 1：系统架构图模板

```
## 系统架构图

### 物理连接
[ASCII 拓扑图]

                    ┌─────────────────────────────┐
                    │      Industrial Network      │
                    │      (EtherNet/IP 100Mbps)  │
                    └──┬──────────────┬────────────┘
                       │              │
              ┌────────┴──┐    ┌──────┴───────┐
              │    PLC    │    │    Robot     │
              │(Master)   │    │  Controller  │
              └────────┬──┘    └──────┬───────┘
                       │              │
              ┌────────┴──┐    ┌──────┴───────┐
              │    HMI    │    │    Vision    │
              │           │    │    System    │
              └───────────┘    └─────────────┘

### 逻辑层级
Level 2: HMI（监控）
Level 1: PLC（Master）+ Robot Controller（Slave）
Level 0: Vision System / Sensors / Actuators
```

---

## Step 2：协议选型输出模板

```markdown
## 通讯协议选型

### 选型矩阵

| 连接 | 协议 | 原因 | 实时性 | 备注 |
|------|------|------|--------|------|
| PLC ↔ Robot | EtherNet/IP | 两端均支持，实时性满足要求 | ~5ms | 循环数据 |
| PLC ↔ Vision | Modbus TCP | 视觉系统标配支持，非实时 | ~20ms | 触发+结果 |
| PLC ↔ HMI | EtherNet/IP | 同一网段，配置简单 | - | 状态监控 |

### 选型依据
[REF: communication/协议手册 §选型指南]
[PAT-030: 协议选型决策规则]
```

---

## Step 3：Master/Slave 定义

```markdown
## Master/Slave 架构

| 角色 | 设备 | 职责 |
|------|------|------|
| Master | PLC | 系统状态机；发出命令；接收状态 |
| Slave 1 | Robot Controller | 执行运动命令；回报完成/错误 |
| Slave 2 | Vision System | 响应触发；输出位置数据 |

状态机实现位置：PLC（Master侧）
```

---

## Step 4：接口定义表模板

```markdown
## 接口信号清单

### PLC → Robot（命令信号）
| 信号名 | 地址/寄存器 | 类型 | 说明 |
|--------|-----------|------|------|
| CMD_GO_HOME | MW100.0 | BOOL | 命令机器人回Home |
| CMD_PICK | MW100.1 | BOOL | 命令执行Pick动作 |
| CMD_PLACE | MW100.2 | BOOL | 命令执行Place动作 |

### Robot → PLC（状态信号）
| 信号名 | 地址/寄存器 | 类型 | 说明 |
|--------|-----------|------|------|
| STS_AT_HOME | MW200.0 | BOOL | 机器人已在Home位置 |
| STS_BUSY | MW200.1 | BOOL | 机器人动作执行中 |
| STS_DONE | MW200.2 | BOOL | 当前动作完成 |
| STS_ERROR | MW200.3 | BOOL | 机器人故障 |
| ERROR_CODE | MW202 | INT | 错误代码 |

### PLC → Vision（触发信号）
| 信号名 | 寄存器 | 类型 | 说明 |
|--------|--------|------|------|
| TRIG_INSPECT | MW300.0 | BOOL | 触发拍照检测 |

### Vision → PLC（结果信号）
| 信号名 | 寄存器 | 类型 | 说明 |
|--------|--------|------|------|
| VIS_DONE | MW400.0 | BOOL | 检测完成 |
| VIS_OK | MW400.1 | BOOL | 产品OK |
| VIS_X | MW402 | REAL | X偏移量 mm |
| VIS_Y | MW404 | REAL | Y偏移量 mm |
| VIS_ANGLE | MW406 | REAL | 角度偏移 deg |
```

---

## Step 5：握手协议设计（来自 PAT-033）

```markdown
## 标准四步握手

时序图：
Master(PLC)          Slave(Robot)
    │                     │
    │──CMD_PICK=1────────→│  Step 1: Master发命令
    │                     │─→ 开始执行
    │←──STS_BUSY=1────────│  Step 2: Slave执行中
    │                     │─→ 完成
    │←──STS_DONE=1────────│  Step 3: Slave完成
    │─→ CMD_PICK=0         │  Step 4: Master清除命令
    │                     │─→ STS_DONE=0（Slave确认）

超时处理：
- Master 发出命令后，2000ms 内未收到 STS_BUSY → 通信超时报警
- STS_BUSY 置位后，30000ms 内未收到 STS_DONE → 动作超时报警
```

---

## 质量检查

- [ ] 是否有完整的系统架构图？
- [ ] 协议选型是否有明确依据？
- [ ] Master/Slave 是否清晰定义？
- [ ] 接口信号是否双向完整？
- [ ] 握手协议是否包含超时处理？
- [ ] 是否考虑了安全互锁信号？

---

## 成长钩子（三大机制）

### Reflexion 钩子（任务后必执行）
- 我的最弱判断是哪个？根因是什么？
- 有没有查阅 agent_ref 但理解有偏差的地方？
- 用户没有问、但我应该主动提出的工程风险是什么？

### Voyager 候选记录
相同类型解法第N次使用时在 memory/core/CORE.md 记录。
第3次成功 → 触发 skills/skill_promotion/SKILL.md 评估。

### Wiki 写入触发
遇到需要深度查阅 agent_ref 的新领域时，
任务后在 wiki/index.md 登记，并创建对应 wiki 页面。

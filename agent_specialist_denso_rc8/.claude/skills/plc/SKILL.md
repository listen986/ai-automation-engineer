# SKILL: PLC Logic Generation — PLC 控制逻辑生成
# Task 3

---

## 触发条件
- "生成PLC程序" / "写梯形图" / "Ladder Logic" / "Structured Text"
- 涉及 PLC 控制逻辑、顺序控制、IO控制

---

## 执行前准备（强制）

### Step 1：确认以下信息（不足时主动询问）
```
□ PLC 品牌和型号（Siemens S7-1200 / Rockwell 5069 / OMRON NX 等）
□ 编程语言：Ladder / ST / FBD / SFC？
□ IO 清单（地址、描述、类型）- 用户提供或需要协助设计
□ 安全等级要求（有无 Safety PLC？SIL等级？）
```

### Step 2：IO 映射表（所有程序必须先输出）

```markdown
## IO 映射表

### 数字输入 (DI)
| 地址 | 变量名 | 描述 | 有效电平 | 来源设备 |
|------|--------|------|---------|---------|
| I0.0 | E_STOP | 急停按钮 | 常闭(NC) | 操作面板 |
| I0.1 | DOOR_SW | 安全门开关 | 常开(NO) | 安全门 |

### 数字输出 (DO)
| 地址 | 变量名 | 描述 | 有效电平 | 目标设备 |
|------|--------|------|---------|---------|
| Q0.0 | MOTOR_EN | 电机使能 | 高电平 | 伺服驱动器 |

### 模拟输入 (AI) / 模拟输出 (AO)
| 地址 | 变量名 | 描述 | 量程 | 工程单位 |
|------|--------|------|------|---------|
```

### Step 3：读取经验
读取 knowledge/patterns/codegen_patterns.md 的 PAT-010~012。

---

## 程序生成结构（强制）

### 结构模板
```
Block 1: 安全互锁（SAFETY INTERLOCK）
Block 2: 状态管理（STATE MACHINE）
Block 3: 顺序控制（SEQUENCE CONTROL）
Block 4: IO控制（IO CONTROL）
Block 5: 报警处理（ALARM HANDLING）
Block 6: 诊断与记录（DIAGNOSTICS）
```

---

## 三种编程语言输出规范

### Ladder Logic（梯形图）文字表示
```
网络 N001 [安全互锁 - 所有输出的前提条件]
-|E_STOP_OK|--|DOOR_CLOSED|--|SYS_READY|--( SAFE_TO_RUN )

网络 N002 [电机启动 - 必须在安全条件下]
-|SAFE_TO_RUN|--|START_CMD|--|/MOTOR_EN|--( MOTOR_EN )  ← 启动
-|MOTOR_EN  |--|/STOP_CMD |--           --( MOTOR_EN )  ← 自保持
```

### Structured Text (ST)
```pascal
(* [SAFETY] Block 1: 安全互锁 *)
SAFE_TO_RUN := E_STOP_OK AND DOOR_CLOSED AND SYS_READY;

(* Block 2: 状态机 *)
CASE MachineState OF
  0: (* IDLE *)
    IF StartCmd AND SAFE_TO_RUN THEN
      MachineState := 10;
    END_IF;
    
  10: (* RUNNING *)
    (* 执行逻辑 *)
    IF StopCmd OR NOT SAFE_TO_RUN THEN
      MachineState := 20;
    END_IF;
    
  20: (* STOPPING *)
    (* 停止逻辑 *)
    MachineState := 0;
    
  99: (* FAULT *)
    (* 故障处理 *)
END_CASE;
```

### Function Block Diagram (FBD)
以文字描述模块连接关系：
```
[E_STOP_OK]─┐
[DOOR_SW]  ─┤─→[AND]─→[SAFE_TO_RUN]─→[AND]─→[MOTOR_EN]
[SYS_RDY]  ─┘          [START_CMD]  ─┘
```

---

## 安全互锁设计规范（强制）

每个输出控制，必须包含：
```
条件1：E-STOP 常闭触点（硬线）
条件2：安全门/围栏状态
条件3：设备就绪信号
条件4：上游完成信号
条件5：无报警状态
→ 所有条件 AND → 才允许输出动作
```

---

## 输出附件：控制逻辑说明

```markdown
## 控制逻辑说明

### 系统状态机
| 状态 | 编号 | 进入条件 | 退出条件 |
|------|------|---------|---------|

### 安全互锁清单
| 互锁名称 | 条件 | 动作 | 复位方式 |
|---------|------|------|---------|

### 报警清单
| 报警编号 | 描述 | 触发条件 | 处理方式 |
|---------|------|---------|---------|
```

---

## 质量检查（输出前自检）

- [ ] IO 映射表是否完整？
- [ ] 安全互锁是否在每个输出的最左侧触点？
- [ ] 是否使用状态机处理顺序逻辑（>3步）？
- [ ] 是否有报警处理逻辑？
- [ ] 同一线圈是否只在一处驱动？

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

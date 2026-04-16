# SHARED_STANDARDS.md
# 跨 Agent 共享标准
# 所有专家 Agent 和总工程师必须遵守的统一规范
# 确保不同 Agent 的输出可以无缝对接

---

## 为什么需要共享标准

专家 Agent 各自独立成长，但它们的输出需要对接。
没有统一标准，PLC专家定义的信号名和机器人专家期待的信号名会不一致，
总工程师就必须手动翻译，成本极高。

**这个文件定义所有 Agent 输出时必须遵守的格式，
使总工程师能直接拼接多个专家的输出。**

---

## 1. 信号命名规范

所有 Agent 定义 IO 信号时，必须使用统一命名格式：

```
[设备前缀]_[方向]_[功能名]_[编号]

设备前缀：
  ROB   机器人
  PLC   PLC
  VIS   视觉系统
  CNV   传送带
  ACT   执行器（气缸、夹爪等）
  SEN   传感器

方向：
  CMD   命令（Master → Slave）
  STS   状态（Slave → Master）
  DAT   数据（双向或无方向性）

示例：
  ROB_CMD_GO_HOME       机器人回Home命令
  ROB_STS_AT_HOME       机器人已在Home状态
  PLC_CMD_START_CYCLE   PLC发出启动周期命令
  VIS_DAT_POS_X         视觉输出X坐标数据
  ACT_CMD_GRIPPER_CLOSE 夹爪关闭命令
```

---

## 2. IO映射表标准格式

所有涉及 IO 的任务，输出必须包含此格式的映射表：

```markdown
## IO映射表

| 信号名（标准命名） | 设备 | 地址/寄存器 | 数据类型 | 有效值 | 说明 |
|------------------|------|-----------|---------|--------|------|
| ROB_CMD_GO_HOME | RC8 | IO[10] | BOOL | 1=命令 | PLC发给机器人 |
| ROB_STS_AT_HOME | RC8 | IO[20] | BOOL | 1=已到位 | 机器人回复PLC |
| VIS_DAT_POS_X   | Keyence | MW402 | REAL | mm | 视觉X偏移 |
```

---

## 3. 握手协议标准（四步）

所有 Agent 设计设备间通讯时，必须使用四步握手：

```
步骤 1：Master 发命令    [信号名]_CMD = 1
步骤 2：Slave 确认接收   [信号名]_ACK = 1（Master等待）
步骤 3：Slave 完成/报错  [信号名]_DONE = 1 或 [信号名]_ERR = 1
步骤 4：Master 清除命令  [信号名]_CMD = 0（Slave随后清除ACK/DONE）

超时定义（必须声明）：
  步骤1→2 超时：500ms（通信超时）
  步骤2→3 超时：[任务相关]ms（动作超时，按实际动作时间设定）
```

---

## 4. 置信度标注规范（统一）

所有 Agent 输出中的置信度标注必须一致：

| 标注 | 含义 | 跨Agent传递时的处理 |
|------|------|-------------------|
| `[REF: 文件 §章节]` | 有手册依据 | 总工程师直接信任 |
| `[WIKI: wiki/页面]` | 有知识库依据 | 总工程师参考，可核实 |
| `[高置信]` | 有充分工程依据 | 总工程师直接信任 |
| `[中置信]` | 合理推断 | 总工程师标记待确认 |
| `[低置信]` | 需验证 | 总工程师必须人工确认后才能进入下一阶段 |
| `[INTERFACE]` | 产生的接口定义 | 总工程师提取并分发给相关Agent |
| `[SAFETY]` | 安全相关 | 总工程师强制人工审核 |

---

## 5. 错误代码共享格式

当一个 Agent 在 `knowledge/mistakes/log.md` 中记录了错误，
如果这个错误具有跨领域价值，使用以下格式标记为"可共享"：

```markdown
## [ERR-NNN] 错误标题
...（标准格式）...
**跨Agent共享**：是
**共享范围**：所有Agent | plc-specialist | robot-specialist
**共享摘要**：[一句话，不含项目私密信息]
```

总工程师定期从各专家的 mistakes/log.md 中提取"可共享"条目，
汇编到团队级别的错误知识库。

---

## 6. 项目上下文传递格式

总工程师向专家发布任务时，必须附带项目上下文：

```yaml
project_context:
  project_id: "PROJ-NNN"
  project_name: "..."
  
  system_overview:
    devices: []           # 项目中所有设备列表
    master_device: ""     # 谁是Master（通常是PLC）
    comm_protocol: ""     # 主通讯协议
    safety_level: ""      # SIL等级
  
  completed_artifacts:    # 其他Agent已完成的输出
    - task_id: "TASK-NNN"
      agent: "plc-specialist-001"
      artifact: "path/to/io_map.md"
      summary: "PLC侧IO定义，包含ROB_CMD_*和ROB_STS_*信号"
  
  constraints:
    - "所有机器人信号地址从IO[10]开始"
    - "握手超时统一使用2000ms"
    - "E-STOP信号优先级最高，不得被软件覆盖"
```

---

## 7. 接口占位符格式

当一个 Agent 等待另一个 Agent 的输出时，在自己的代码/文档中使用占位符：

```
{{INTERFACE:来源Agent:信号名:说明}}

示例：
{{INTERFACE:plc-specialist:PLC_CMD_START_CYCLE:等待PLC确认启动周期信号地址}}
{{INTERFACE:vision-specialist:VIS_DAT_POS_X:等待视觉输出X坐标寄存器地址}}
```

总工程师收到含占位符的输出时，负责协调相关 Agent 填充，
并在最终文档中替换所有占位符。

---

## 8. 协议遵从性声明

每个专家 Agent 的 AGENTS.md 必须包含：

```markdown
## 协作协议遵从

本 Agent 遵守 collab/shared_protocol/SHARED_STANDARDS.md v1.0
- 信号命名：遵守第1节规范
- IO映射表：遵守第2节格式
- 握手协议：遵守第3节四步握手
- 置信度标注：遵守第4节规范
- 接口占位符：遵守第7节格式
```

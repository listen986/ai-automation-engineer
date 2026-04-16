# wiki/index.md — DENSO RC8 知识库总索引
# Karpathy LLM Wiki 模式 · 由 Agent 综合编写 · 交叉引用网络
# 专注：DENSO RC8 控制器 + PacScript 编程语言

---

## Wiki 定位

| | agent_ref/ | wiki/ |
|---|---|---|
| 内容 | DENSO 官方原文，完整手册 | Agent 综合提炼，要点+规则+案例 |
| 作者 | DENSO 官方 | Agent（人工校正）|
| 读取时机 | 执行具体指令时精确查阅 | 任务开始时快速定向 |
| 修改 | 不可修改 | 持续迭代 |

---

## 页面索引

### pacscript/ — PacScript 语言知识

| 页面 | 内容摘要 | 状态 |
|------|---------|------|
| [[pacscript/motion_commands]] | Move/MoveL/MoveC/Approach/Depart 要点 | 待创建 |
| [[pacscript/speed_control]] | Speed/Accel/CurSpd 设置与分区策略 | 待创建 |
| [[pacscript/io_control]] | Set IO / Wait IO / 执行器控制模式 | 待创建 |
| [[pacscript/multitask]] | TakeArm/GiveArm / 多任务架构 | 待创建 |
| [[pacscript/error_handling]] | On Error / 错误恢复模式 | 待创建 |
| [[pacscript/variables]] | 变量类型 / 位置变量 / 共享变量 | 待创建 |

### rc8/ — RC8 控制器知识

| 页面 | 内容摘要 | 状态 |
|------|---------|------|
| [[rc8/architecture]] | RC8 控制器架构 / 任务模型 / 启动流程 | 待创建 |
| [[rc8/io_system]] | RC8 IO 地址映射 / 数字IO / 模拟IO | 待创建 |
| [[rc8/communication]] | EtherNet/IP 配置 / 串口 / 通讯模式 | 待创建 |
| [[rc8/safety_functions]] | E-STOP / 安全速度 / 安全IO | 待创建 |
| [[rc8/teach_pendant]] | TP 操作 / 程序管理 / 调试模式 | 待创建 |

### patterns/ — 工程模式知识

| 页面 | 内容摘要 | 状态 |
|------|---------|------|
| [[patterns/pick_and_place]] | 标准 Pick & Place 程序结构 | 待创建 |
| [[patterns/rc8_plc_handshake]] | RC8 ↔ PLC 四步握手完整模板 | 待创建 |
| [[patterns/vision_correction]] | RC8 ↔ Vision 坐标补偿流程 | 待创建 |

### errors/ — RC8 错误案例

| 页面 | 内容摘要 | 状态 |
|------|---------|------|
| （首次遇到并确认根因的错误后创建） | | |

---

## Wiki 写作规范

```markdown
# [[页面名]] — 标题

**摘要**：[一句话，<50字]
**来源**：agent_ref/[文件] §[章节] | patterns/[PAT-NNN] | SESSION-NNN
**相关页面**：[[页面A]] · [[页面B]]
**最后更新**：YYYY-MM-DD

---

## 核心内容
[Agent综合编写，不抄原文，提炼要点]

## 关键判断规则
[从 pacscript_patterns.md 提炼]

## 常见错误与根因
[从 mistakes/log.md 提炼]

## 待解问题
[来自 open_questions/]
```

---

## Lint 检查清单

每 10 次任务执行：
- [ ] wiki 页面内容与 agent_ref/ 原文是否矛盾？
- [ ] 有无孤立页面（零被引用）？
- [ ] PacScript 语法描述版本是否与当前手册一致？
- [ ] errors/ 页面是否及时更新新发现的根因？

问题记录至 `knowledge/lint/issues.md`

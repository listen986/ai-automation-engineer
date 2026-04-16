# knowledge/CLAUDE.md — Knowledge Base 使用指南 v2.0
# 融合：CoALA四层记忆 · Karpathy Wiki · Reflexion萃取

---

## 记忆层级与读取顺序

```
任务开始时读取顺序：

1. memory/core/CORE.md           必读·当前身份状态·约束
2. memory/episodic/recent.md     必读·最近5次任务教训
3. wiki/index.md                 按需·知识地图定向
4. wiki/[相关页面]               按需·语义记忆精读
5. knowledge/patterns/[文件]     按需·程序规则匹配
6. knowledge/mistakes/log.md     按需·错误案例筛选
7. agent_ref/[具体手册]          执行时·官方语法核实
```

越靠上越常读，越靠下越精确读。
wiki/ 用于快速定向，agent_ref/ 用于权威核实。

---

## 写入决策树

```
Reflexion 完成后判断写入目标：

发现可复用判断规则  → knowledge/patterns/ + wiki/patterns/
发现错误根因        → knowledge/mistakes/log.md + wiki/errors/
发现新待解问题      → knowledge/open_questions/pending.md
接触新设备          → wiki/devices/[设备].md + domain_map/
某解法第3次成功     → 触发 skills/skill_promotion/ 流程
完成任务            → memory/episodic/recent.md（必须）
                      knowledge/episodic/session_log.md（必须）
                      memory/core/CORE.md 统计数字（必须）
```

---

## 写入格式

### patterns/ — 判断规则

```markdown
## [PAT-NNN] 规则标题
**适用场景**：___
**判断规则**：当遇到[情境]时，优先[做法]，因为[工程逻辑]
**例外条件**：当[条件]时，改用[替代]
**置信度**：高/中
**验证次数**：N次（SESSION-NNN）
**关联wiki**：[[wiki/patterns/页面]]
**关联错误**：ERR-NNN
---
```

### mistakes/log.md — 错误案例

```markdown
## [ERR-NNN] 错误标题
**日期**：YYYY-MM-DD · SESSION-NNN
**任务类型**：qa/codegen/plc/explain/diagnosis/documentation/integration
**标签**：#领域 #错误类型
**场景**：___
**错误表现**：___
**根本原因**：___（深挖，不接受表面原因）
**解法**：___
**预防机制**：___
**已写入wiki**：是 [[wiki/errors/页面]] / 否
**已写入patterns**：是 [PAT-NNN] / 否
---
```

### episodic/session_log.md — 任务情景

```markdown
## [SESSION-NNN] [任务类型] · YYYY-MM-DD
**任务**：___
**资料使用**：agent_ref/___ | wiki/___ | patterns/___
**假设vs结果**：___
**Reflexion最弱判断**：___（根因：___）
**萃取内容**：ERR-NNN / PAT-NNN / wiki/页面 / OQ-NNN / 无
**Voyager记录**：解法"___"第N次（N/3）
---
```

### wiki/[类别]/[页面].md — 综合知识页

```markdown
# [[页面名]] — 标题

**摘要**：[一句话，<50字]
**来源**：agent_ref/[文件] | patterns/[PAT-NNN] | SESSION-NNN
**相关页面**：[[页面A]] · [[页面B]]
**最后更新**：YYYY-MM-DD

---

## 核心内容
[LLM综合编写，提炼要点，不抄原文]

## 关键判断规则
[从patterns/提炼，可直接应用]

## 常见错误与根因
[从mistakes/提炼，含根因]

## 待解问题
[来自open_questions/]
```

---

## 禁止事项

```
✗ 自由格式写入（破坏可检索性）
✗ 将 agent_ref/ 原文复制进 wiki/
✗ 删除 mistakes/log.md 任何条目
✗ 创建无交叉引用的孤立 wiki 页面
✗ 不执行 Reflexion 直接写入
```

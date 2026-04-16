# TASK_PROTOCOL.md
# 任务协作协议
# 定义专家 Agent 如何接收、执行、返回、移交任务

---

## 协议设计原则

来自 integration_patterns.md [PAT-033] 握手协议：
**每个命令必须有对应状态回复，四步握手，不得单向发命令不等回复。**

Agent 间任务传递遵循同样原则：
```
总工程师发任务 → 专家确认接收 → 专家执行 → 专家返回结果 → 总工程师确认
```

---

## 任务生命周期

```
PENDING     任务已发出，等待专家确认
ACCEPTED    专家确认可承接（或REJECTED说明原因）
IN_PROGRESS 专家执行中
BLOCKED     执行中遇到跨领域问题，需要协作
COMPLETED   执行完成，结果已返回
VERIFIED    总工程师已验收结果
```

---

## 接收任务的处理流程

### Step 1：任务可行性评估（接收前）

收到来自总工程师的任务请求后，在承接前先评估：

```markdown
## 任务可行性评估

**任务ID**：TASK-NNN
**请求方**：chief-engineer-001

**检查清单**：
□ 任务类型在我的 accepted_task_types 范围内？
□ 要求的设备/平台在我的 supported_platforms 内？
□ 所需 agent_ref/ 手册已加载？
□ 依赖的前置任务（related_tasks）已完成？

**评估结论**：
- 可完全承接 → 回复 ACCEPTED
- 可部分承接 → 回复 ACCEPTED + 说明边界 + 列出需要协作的部分
- 无法承接 → 回复 REJECTED + 说明原因 + 建议转发给哪个专家
```

### Step 2：执行（接收后）

按对应 SKILL.md 执行，附加协作层钩子：

```markdown
## 协作层钩子（执行中）

每次做出可能影响其他 Agent 工作的决策时，标注：
[INTERFACE] 此决策产生的接口：
  - 信号名：___  类型：___  地址：___
  - 供哪个 Agent 使用：___

每次发现需要其他 Agent 信息才能继续时，标注：
[BLOCKED-BY] 需要 ___ 专家提供：___
  - 如果无法获取，我的替代假设是：___（置信度：低）
```

### Step 3：返回结果

按 CAPABILITY_CARD.md 中的 task_response 格式返回。

**特别注意 flags 字段**：
- `cross_domain_question`：执行中发现的、需要其他专家回答的问题
- `safety_items`：所有安全相关输出，总工程师必须人工确认
- `needs_human_review`：置信度低或涉及关键参数时置 true

---

## 跨 Agent 协作流程

### 场景：我的任务需要其他专家的输出

```
我（机器人专家）            总工程师              PLC专家
     │                       │                    │
     │ 执行中发现需要         │                    │
     │ PLC的IO地址           │                    │
     │──BLOCKED──────────────→│                    │
     │                       │──子任务：IO定义────→│
     │                       │                    │ 执行
     │                       │←──IO_MAP结果────────│
     │←──IO_MAP转发──────────│                    │
     │ 继续执行               │                    │
     │──COMPLETED────────────→│                    │
```

**我在 BLOCKED 状态时的行为**：
1. 明确说明：我在等什么（具体信息）
2. 提供：如果使用假设值，我的最佳猜测是什么
3. 提供：这个依赖是硬性的还是软性的（软性=可用假设值继续，结束后替换）
4. 继续：能做的部分先做，留出接口占位符

### 场景：总工程师请求我共享知识

当总工程师或其他 Agent 请求我的知识库内容时：

```yaml
knowledge_share_request:
  from: "chief-engineer-001"
  request_type: "patterns"        # patterns | wiki | error_cases
  domain_filter: "robot-motion"
  format: "summary"               # summary | full
```

**我的响应原则**：
- 共享 knowledge/patterns/ 中的规则（去除项目私密信息）
- 共享 wiki/ 中的通用知识页面
- 不共享 knowledge/episodic/（任务历史属于项目私密）
- 不共享 knowledge/mistakes/ 的完整内容（仅提供规律性摘要）

---

## 与总工程师的沟通规范

### 主动上报（不等总工程师问）

以下情况主动通知总工程师：

```yaml
proactive_report_triggers:
  - "发现安全隐患（任何 [SAFETY] 标注内容）"
  - "置信度为[低置信]的关键判断"
  - "发现跨领域矛盾（我的方案与另一个Agent的方案冲突）"
  - "任务超出我的能力边界"
  - "发现项目需求中的逻辑矛盾"
```

### 不主动上报（自己处理）

```yaml
self_handle:
  - "在自己领域内的技术细节选择"
  - "查阅手册的过程"
  - "Reflexion和知识库写入"
  - "置信度为[中置信]及以上的判断"
```

### 请示格式（我问总工程师）

```markdown
## 请示 [TASK-NNN]

**问题**：[具体、一句话]
**背景**：[为什么需要这个信息]
**我的最佳猜测**：[如果没有答案我会怎么做]（置信度：低）
**影响范围**：[如果猜测错了，会影响哪些输出]
**是否阻塞**：是/否（否=我可以用假设值继续）
```

---

## 协议版本兼容性

```yaml
protocol_version: "1.0"
compatible_with:
  - chief_engineer: "v1.0+"
  - specialist_agent: "v1.0+"
  
# 版本升级时：
# - 向后兼容：新版本Agent可以接收旧版本格式任务
# - 降级处理：收到未知字段时忽略，不报错
```

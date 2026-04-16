# SKILL: Robot Code Generation — 机器人程序生成
# Task 2

---

## 触发条件
- "生成…程序" / "写一个…代码" / "帮我写…"
- 涉及机器人控制器程序（KUKA KRL / DENSO PacScript / FANUC TP / ABB RAPID 等）

---

## 执行前准备（强制）

### Step 1：确认目标控制器
**必须明确以下信息，不足时主动询问用户：**
```
□ 控制器品牌和型号（如 DENSO RC8 / KUKA KR C4 / FANUC R-30iB）
□ 编程语言版本（如 PacScript 4.x / KRL / TP）
□ 对应手册是否在 agent_ref/controllers/ 中
```

### Step 2：加载手册
确认控制器后，从 agent_ref/controllers/ 加载对应手册相关章节。
**禁止不查手册直接生成代码。**

### Step 3：读取已有经验
读取 knowledge/patterns/codegen_patterns.md，检查 PAT-001~004 适用规则。

---

## 代码生成结构（强制）

每次生成机器人程序，必须包含以下六个部分：

```
Part 1: 程序头注释
Part 2: 全局变量/位置点定义
Part 3: 安全初始化块
Part 4: 主逻辑
Part 5: 错误处理块
Part 6: 程序说明表
```

### Part 1：程序头注释模板
```
'========================================
' 程序名称：[名称]
' 控制器  ：[品牌 型号]
' 编程语言：[语言/版本]
' 功能描述：[简要说明]
' 作者    ：AI Automation Engineer
' 日期    ：[日期]
' 版本    ：v1.0
' 依据    ：[REF: 手册名 §章节]
'========================================
```

### Part 2：位置/变量定义
- 所有位置点在程序头集中定义，命名清晰（P_Home, P_Pick, P_Place）
- 速度变量统一定义（SPD_HIGH, SPD_LOW, SPD_APPROACH）

### Part 3：安全初始化块
```
' [SAFETY] 安全初始化
' E-STOP 状态检查
' 速度限制设置
' 回 Home 确认
```

### Part 4：主逻辑
- 每个功能段加注释标题
- 非显而易见的参数值注释原因
- [SAFETY] 标注所有安全相关行

### Part 5：错误处理块
- 至少包含：错误捕获 + 停机 + 报警输出

### Part 6：程序说明表（代码后附）
```markdown
## 程序说明

| 项目 | 内容 |
|------|------|
| 控制器 | ___ |
| 语法依据 | [REF: ___] |
| 位置点数量 | ___ |
| 预计周期时间 | ___ |
| 安全功能 | E-STOP响应 / 速度限制 / ___ |
| 注意事项 | ___ |
```

---

## 质量检查（输出前自检）

- [ ] 语法是否来自对应控制器手册（REF标注）？
- [ ] 是否包含安全初始化块？
- [ ] 速度是否有分区设置？
- [ ] 错误处理是否完整？
- [ ] 注释密度是否足够？
- [ ] 是否混用了其他控制器的语法？

---

## 支持的控制器语言

| 控制器 | 语言 | 手册位置 |
|--------|------|---------|
| DENSO RC8 | PacScript | agent_ref/controllers/ |
| KUKA KR Cx | KRL | agent_ref/controllers/ |
| FANUC R-30iB | TP / Karel | agent_ref/controllers/ |
| ABB IRC5 | RAPID | agent_ref/controllers/ |
| Yaskawa YRC1000 | INFORM | agent_ref/controllers/ |

手册未加载时，标注 [低置信] 并提示用户上传手册。

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

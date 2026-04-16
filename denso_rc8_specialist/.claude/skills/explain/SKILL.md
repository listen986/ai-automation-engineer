# SKILL: Code Explanation — 工业程序解释
# Task 4

---

## 触发条件
- "解释这段代码" / "这行是什么意思" / "帮我看看这个程序"
- 用户粘贴机器人程序或PLC程序，要求理解

---

## 执行前准备

### Step 1：识别程序语言
```
扫描程序特征，判断语言类型：
- Sub Main / Move / TakeArm  → PacScript (DENSO)
- DEF / PTP / LIN / FOLD    → KRL (KUKA)
- J P[1] 100% FINE           → TP (FANUC)
- PROC / MoveJ / MoveL       → RAPID (ABB)
- NETWORK / |---| |---|       → Ladder Logic
- IF / CASE / WHILE           → Structured Text
```

### Step 2：加载对应手册
确认语言后，从 agent_ref/ 加载对应手册，用于核对指令含义。

---

## 输出结构（强制）

### Level 1：整体逻辑说明
```markdown
## 程序整体说明

**语言**：[控制器/语言]
**功能**：[一句话描述程序目的]
**结构**：[程序的主要组成部分]
**执行流程**：
  1. ___
  2. ___
  3. ___
```

### Level 2：逐行/逐块解释

对齐格式输出：
```
原代码                              | 解释
------------------------------------|------------------------------------------
Move P, P_Home, S=100              | 以PTP插补运动到Home点，速度100%
                                    | [REF: motion/PacScript §Move p.45]
TakeArm Keep=0                     | 获取机器人轴控制权，不保持独占
                                    | [高置信] 多任务控制场景常用指令
IO[10] = 1                         | 将数字输出10置高，触发下游设备
```

### Level 3：关键部分深度说明
对程序中重要或复杂的部分，额外提供：
- 为什么这样写（设计意图）
- 这段逻辑的边界条件
- 潜在风险或改进建议

---

## 不确定指令处理规则

遇到不确定含义的指令：
1. 标注 `[低置信 - 需查手册]`
2. 说明推测依据
3. 明确建议用户确认

**禁止对不确定指令给出肯定解释。**

---

## 输出示例

```markdown
## 程序整体说明

**语言**：DENSO PacScript
**功能**：从 Pick 点抓取工件，搬运至 Place 点放置，循环执行
**结构**：初始化 → 等待触发 → 抓取 → 搬运 → 放置 → 回 Home

**执行流程**：
1. 程序启动，初始化速度和 IO 状态
2. 等待外部触发信号（IO[5]=1）
3. 运动至 Pick 点上方（Approach），降速接近
4. 夹爪关闭，确认抓取
5. 提升离开，运动至 Place 点
6. 放置工件，夹爪打开
7. 返回 Home 点，等待下次触发

---

## 逐行解释

| 代码 | 解释 | 依据 |
|------|------|------|
| Speed 50 | 设置全局速度为额定速度的50% | [REF: motion/PacScript §Speed] |
| Wait IO[5]=1 | 等待输入IO5变为高电平（外部触发） | [高置信] |
| Approach P, P_Pick, 50 | 向P_Pick点的-Z方向50mm处运动 | [REF: motion/PacScript §Approach] |
```

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

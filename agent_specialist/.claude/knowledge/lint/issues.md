# lint/issues.md — Wiki 质量问题记录
# Karpathy Wiki Lint 模式 · 防止知识库腐烂

> 每10次任务或发现矛盾时执行 Lint 检查。
> 发现的问题记录在此，修复后标记状态。

---

## Lint 检查项目

每次检查时逐项确认：
```
□ wiki 页面之间是否有相互矛盾内容？
□ 有无孤立页面（0个被引用）？
□ 有无内容版本低于当前 agent_ref（设备升级后）？
□ open_questions 中有无已有答案但未迁移的问题？
□ knowledge/patterns 规则与 wiki/patterns 是否同步？
□ mistakes/log.md 中有无已解决但 wiki 未更新的条目？
```

---

## 问题记录

```markdown
## [LINT-NNN] 问题描述
**发现时间**：YYYY-MM-DD（第N次Lint，SESSION-NNN触发）
**问题类型**：矛盾/孤立页面/版本过时/未迁移/不同步
**涉及文件**：wiki/___  vs  ___
**问题描述**：___
**修复方案**：___
**状态**：待修复 / 已修复（YYYY-MM-DD）
---
```

---

## 问题列表

<!-- 首次部署为空，随 Lint 执行积累 -->

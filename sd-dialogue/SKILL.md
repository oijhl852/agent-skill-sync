---
name: sd-dialogue
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: 对话戏分镜生成 — 谁说话给谁镜头、镜头分配、长台词穿插反应。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-dialogue — 对话分镜

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

专注对话戏的分镜头设计。

## 铁律

**铁律0：谁说话给谁镜头** — 台词和画面同步，不在台词说到一半时切给静默角色。

**铁律1：长台词必须穿插反应镜头** — 超过一段话时，中间切给听者无台词反应镜头。

**铁律2：切镜头必须等话说完** — 只在。！？之后切，不在句中打断。

**铁律3：过长台词先拆句** — 超过8秒的独角戏，拆2-3句，句间插反应镜头。

## 镜头分配

- 主角屏占比：两个主要角色各占 ~40%
- 配角：每段最多1个镜头
- 禁止同人连续切镜
- 动作镜头：1-2秒独立镜头

## 对话时间轴模板

```
建立站位 → 说话者完整说一句 → 听者反应 → 回应或留钩子
```

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync

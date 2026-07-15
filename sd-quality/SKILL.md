---
name: sd-quality
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: 最终质量检查 — 保真/密度/约束/衔接。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-quality — 质量检查

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

对生成的提示词进行最终审查。

## 检查清单

### 格式
- [ ] 每个镜头以 `△` 开头
- [ ] 运镜、景别写具体
- [ ] 对白标注正确（os/vo/情绪不混用）

### 保真
- [ ] 原剧本台词没有丢失或重复
- [ ] 核心剧情未被改写
- [ ] OS/VO保留在时间轴

### 密度
- [ ] 每段5-6镜，不超过此密度
- [ ] 15秒没有塞过多内容
- [ ] 主角合计~80%屏幕时间

### 技术标签
- [ ] 11标签齐全（风格/画幅/镜头/环境/布光/光影/色调/特效/运镜/构图/表演）
- [ ] 反向约束完整（禁止越轴/不生成多个相同角色/眨眼/无文字）

### 衔接
- [ ] 尾帧描述与下段首帧一致
- [ ] 多段视频衔接自然

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync

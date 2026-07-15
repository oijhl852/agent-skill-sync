---
name: sd-inject
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: API 注入引擎 — 凭证提取、segment/list、segment/update、注入验证。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-inject — 注入引擎

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

通过 Seedance 平台 API 将提示词注入面板编辑器。

## 致命警告

🔴 **绝不调用 `segment/del`** — 永久删除片段并切断已生成视频的链接。

🔴 **DOM 操作不持久** — Vue 从 API 数据重新渲染。唯一可靠方法是通过 `segment/update` API。

## 工作流程

### 1. 提取 API 凭证

```javascript
const ticket = localStorage.getItem('ticket_production')?.replace(/"/g, '');
const userId = JSON.parse(localStorage.getItem('user') || '{}')?.info?.userid;
const projectId = new URL(window.location.href).searchParams.get('project_id');
const partNo = new URL(window.location.href).searchParams.get('part_no');
```

### 2. 获取片段列表

```javascript
const resp = await fetch(
  `https://service.fujunhn.cn/api/v1/aigc/segment/list?ticket=${ticket}&user_id=${userId}&project_id=${projectId}&part_no=${partNo}&_t=${Date.now()}`
);
const segments = (await resp.json()).data.segments;
```

### 3. 注入提示词

```javascript
const resp = await fetch(
  `https://service.fujunhn.cn/api/v1/aigc/segment/update?ticket=${ticket}&user_id=${userId}&_t=${Date.now()}`,
  {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({
      id: segmentId,
      project_id: projectId,
      part_no: partNo,
      content: contentHtml
    })
  }
);
const result = await resp.json();
```

### 4. 验证注入

```
1. 刷新页面 (navigate_page type="reload")
2. switchToSegment(n) 切换到目标片段
3. 检查 chip 数量
```

## 注入后提醒

- 将片段时长设为15s（默认4s）
- 目视检查 chip 渲染
- 仅收到指令时注入下一段

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync

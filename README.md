# Seedance Workflow Skills

Reasonix 技能包 —— AI 视频短剧制作全流程。

## 技能列表

| 技能 | 文件 | 用途 |
|------|------|------|
| awesome-seedance | `awesome-seedance/SKILL.md` | 脚本→分镜生成，Seedance 2.0 提示词全流程 |
| seedance-browser-injector | `seedance-browser-injector/SKILL.md` | Chrome DevTools MCP 浏览器注入 |

## 安装

将整个目录放到 Reasonix 的 skills 路径下：

```
~/.reasonix/skills/
├── awesome-seedance/
└── seedance-browser-injector/
```

或在 `reasonix.toml` 的 `[skills]` 中配置：

```toml
[skills]
paths = ["~/seedance-skills"]
```

## 更新日志

- 2026-06-08: 初始版本，含完整面板元素参考、对话铁律、分段规范

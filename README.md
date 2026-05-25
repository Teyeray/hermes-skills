# Hermes Daily Scheduler Skills

个人用的 Hermes Agent 定时任务 skills，配合 Dida365 实现每日任务提醒、时间规划、完成追踪。

## Skills

| Skill | 触发时间 | 功能 |
|-------|---------|------|
| `daily-morning-digest` | 每天 08:00 | 今日待办 + 时间安排 + 风险提醒 |
| `hourly-task-reminder` | 每天 9-21 点/每小时 | 到点提醒做事 + 过点重排 |
| `daily-evening-review` | 每天 20:00 | 今日总结 + 未完成重排到明天 |
| `weekly-review` | 每周日 20:00 | 本周回顾 + 下周预测 |

## 数据流

```
Dida365 (任务源)
    ↕ MCP API
Hermes Skills (逻辑处理)
    ↕ send_message
微信 (通知)
```

## 依赖

- Hermes Agent (已安装)
- Dida365 MCP Server (已在 Hermes 中配置)
- iLink Bot 微信渠道 (已在 Hermes Gateway 中配置)

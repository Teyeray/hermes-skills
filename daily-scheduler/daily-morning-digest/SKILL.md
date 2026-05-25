---
name: daily-morning-digest
description: "每天08:00发送今日日程、本周概览和天气到微信"
category: daily-scheduler
---

# daily-morning-digest

每天早晨分析 Dida365，总结今日安排和本周概览，加上天气，推送到微信。

> ⚠️ 本 skill 由 cronjob `morning-digest`（每天 08:00）加载执行。
>
> **关键规则：你的最终回复就是发送到微信的消息本身。**
> ❌ 不要在回复中包含思考过程、分析步骤、代码、JSON
> ✅ 回复内容 = 用户将在微信上看到的纯消息
>
> 全部任务都没有时回复 `[SILENT]`。

---

## 执行步骤

### Step 1: 获取今日任务

```
mcp_dida365_list_undone_tasks_by_time_query(query_command="today")
```

### Step 2: 获取本周其他任务

计算本周范围（Asia/Shanghai），调用：
```
mcp_dida365_filter_tasks(filter={
  "startDate": "<本周一 00:00:00+08:00>",
  "endDate": "<本周日 23:59:59+08:00>"
})
```

### Step 3: 获取天气

```
curl -s "wttr.in/Shanghai?format=%C+%t&lang=zh"
```

如果失败就跳过天气。

### Step 4: 统计各项目未完成任务数

用 `mcp_dida365_list_projects` 获取所有项目，统计每个项目下有多少未完成任务。

### Step 5: 组装最终回复

```
☀️ 早安！{月/日 周X}  🌤 {天气}

━━━ 今日安排 ━━━━━━━━━━━━━
□ {任务A}（截止 {时间}）
□ {任务B}

━━━ 本周还有 ━━━━━━━━━━━━━
□ {任务C}（{月/日} 截止）
□ {任务D}

━━━ 项目一览 ━━━━━━━━━━━━
📚 学习 & 研究：{N} 个待办
🔍面试prepare：{N} 个
⚙️ 系统 & 维护：{N} 个
...

━━━
我会逐个问你每个任务的安排细节 👋
```

### Step 6: 完成

---

## 注意事项

- 最终回复 = 纯微信消息
- 以 `☀️ 早安！` 开头
- **不要修改任何 Dida365 数据**（只读），所有安排由用户回复后处理

---
name: hourly-task-reminder
description: "每半小时检查一次，找没问过的任务逐个问用户"
category: daily-scheduler
---

# hourly-task-reminder

每半小时（08:30-21:30）检查所有项目中的所有未完成任务，
找到一个还没问过用户的任务，发一条消息询问细节。

> ⚠️ 本 skill 由 cronjob `hourly-check`（每半小时）加载执行。
>
> **关键规则：你的最终回复就是发送到微信的消息内容。**
>
> 如果所有任务都已问过，回复 `[SILENT]` 静默跳过。

---

## 状态标记约定

任务是否已问过，通过 `content` 字段判断：
- **已问过**：content 包含 `##state:asked:YYYY-MM-DD##`
- **未问过**：content 不包含上述标记

当用户在微信上回复后，微信 session 中的 AI 会更新任务的 content 加上这个标记。

---

## 执行步骤

### Step 1: 获取所有项目的所有未完成任务

```
mcp_dida365_list_projects
```

对每个项目（排除已关闭的），调用：
```
mcp_dida365_get_project_with_undone_tasks(project_id="xxx")
```

收集所有未完成的任务。

### Step 2: 找第一个没问过的任务

遍历所有任务，检查每个任务的 `content` 字段：
- 如果 `content` 包含 `##state:asked:` → 已问过，跳过
- 如果 `content` **不包含** → 未问过，选中它

**找到 → 跳到 Step 3**
**没找到（全部已问过）→ 回复 `[SILENT]`**

### Step 3: 发消息问细节

回复一条纯消息：

```
📋 来安排一个任务～

{任务标题}

关于这个任务，我想了解：
• 你每次打算花多久时间？
• 想安排在什么时间？（比如每周二四上午、每天下午等）
• 想变成习惯定期做吗？

回复我就行，慢慢来，下一个等会儿再问你 👋
```

### Step 4: 完成

---

## 注意事项

- 一次只问一个任务
- 最终回复 = 微信消息，纯文本
- 如果全部已问过 → `[SILENT]`
- 用户随时可以在微信上回复你，AI 会在对话中继续处理
- 处理完成后会在 content 中写 `##state:asked:##` 标记
- 所以下一半小时检查时会跳过这个任务，继续问下一个

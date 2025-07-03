---
template: Daily
date: <% tp.date.now("YYYY-MM-DD") %>
day_of_week: <% tp.date.now("dddd") %>
tags: 
status: pending
source:
---
# 今日の予定 (Agenda)
- [ ] 

---
# 今日のタスク (Tasks)
- [ ] 

---

# 昨日の振り返り (Yesterday Review)
## 達成したこと
- 
# 修正したいこと
- 

---

# 日記・メモ (Journal / Notes)
> 自由に記録

---

# 関連リンク・ノート (Links)
- 📂 Projects: [[MOC]]
- 📂 Weekly Review: [[01-weekly/<% tp.date.now("YYYY-[W]WW") %> Weekly Review]]
- その他: 

---

# ウィジェット
## **Dataview**

#### *Daily*
```dataview
TABLE date AS 日付, day_of_week AS 曜日, status AS ステータス
FROM "00-daily"
WHERE template = "Daily" AND status = "pending"
SORT date ASC
```

#### *Inbox*
```dataview
TABLE date AS 日付, status AS ステータス
FROM "10-inbox"
WHERE status = "pending"
SORT date ASC
```

Task
```dataview
table
  task.text     as "タスク内容",
  task.due      as "期限"
from "00-daily"
flatten file.tasks as task
where task.completed = false
sort task.due asc
```

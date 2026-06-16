# 情境技能路由

> [← 返回中文說明](../中文.md) · [English README](../README.md)

根據你**現在遇到的情境**，快速找到對應的 skill。

---

## 開始之前

| 情境 | 使用技能 |
|------|---------|
| 第一次用這套 skills，需要設定 repo | [`/setup-matt-pocock-skills`](../skills/engineering/setup-matt-pocock-skills/SKILL.md) |
| 不知道自己要建什麼、需求還很模糊 | [`/grill-me`](../skills/productivity/grill-me/SKILL.md) |
| 有程式碼相關問題、需要同時更新文件 | [`/grill-with-docs`](../skills/engineering/grill-with-docs/SKILL.md) |

---

## 寫程式途中

| 情境 | 使用技能 |
|------|---------|
| 要開始實作某個功能或修 bug | [`/tdd`](../skills/engineering/tdd/SKILL.md) |
| 要把計畫 / 討論轉成 GitHub issues | [`/to-issues`](../skills/engineering/to-issues/SKILL.md) |
| 要產出 PRD（產品需求文件） | [`/to-prd`](../skills/engineering/to-prd/SKILL.md) |
| 看到一段程式碼，不知道它在整個系統裡是什麼角色 | [`/zoom-out`](../skills/engineering/zoom-out/SKILL.md) |
| 需要快速建立原型來驗證想法 | [`/prototype`](../skills/engineering/prototype/SKILL.md) |

---

## 出問題了

| 情境 | 使用技能 |
|------|---------|
| Bug 很難追、或有效能退化 | [`/diagnose`](../skills/engineering/diagnose/SKILL.md) |
| Issue 堆積如山，需要分類整理 | [`/triage`](../skills/engineering/triage/SKILL.md) |

---

## 程式碼品質與架構

| 情境 | 使用技能 |
|------|---------|
| 程式碼庫越來越亂、架構開始腐敗 | [`/improve-codebase-architecture`](../skills/engineering/improve-codebase-architecture/SKILL.md) |
| 想為 dangerous git 指令設防護網 | [`/git-guardrails-claude-code`](../skills/misc/git-guardrails-claude-code/SKILL.md) |

---

## Agent 行為調整

| 情境 | 使用技能 |
|------|---------|
| Agent 回應太囉唆，浪費 token | [`/caveman`](../skills/productivity/caveman/SKILL.md) |
| 要把工作交接給另一個 Agent 繼續 | [`/handoff`](../skills/productivity/handoff/SKILL.md) |

---

## 學習與擴充

| 情境 | 使用技能 |
|------|---------|
| 想學某個新技術 / 概念（多 session 學習） | [`/teach`](../skills/productivity/teach/SKILL.md) |
| 想自己寫一個新的 skill | [`/write-a-skill`](../skills/productivity/write-a-skill/SKILL.md) |

---

## 快速決策樹

```
你想做什麼？
│
├─ 釐清需求 → /grill-me 或 /grill-with-docs
│
├─ 寫程式
│   ├─ 從需求開始 → /to-prd → /to-issues → /tdd
│   └─ 直接 debug → /diagnose
│
├─ 改善現有程式碼
│   ├─ 架構問題 → /improve-codebase-architecture
│   └─ 看不懂某段 code → /zoom-out
│
├─ 管理工作流程
│   ├─ 整理 issues → /triage
│   ├─ 交接工作 → /handoff
│   └─ Agent 太囉唆 → /caveman
│
└─ 學習 / 擴充
    ├─ 學新東西 → /teach
    └─ 建新 skill → /write-a-skill
```

---

> 完整技能說明請見 [中文說明](../中文.md)

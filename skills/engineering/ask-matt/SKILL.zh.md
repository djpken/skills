---
name: ask-matt
description: 詢問哪個 skill 或流程適合你的情況。本 repo 中所有使用者呼叫 skill 的路由器。
disable-model-invocation: true
---

# Ask Matt

你不會記住所有 skill，所以就問吧。

**流程（flow）** 是穿越各 skill 的路徑。大多數路徑沿著一條**主流程**前進，兩條**入口道**匯入其中。其他的都是獨立 skill。

## 主流程：想法 → 出貨

大多數工作走的路線。你有一個想法，想把它做出來。

1. **`/grill-with-docs`** — 透過訪談磨練想法。**有程式碼庫**時從這裡開始：它有狀態，會將學到的東西保留在 `CONTEXT.md` 和 ADR 中。（沒有程式碼庫？用 `/grill-me`——見獨立項目。）
2. **分支——你能在對話中解決所有問題嗎？** 如果某個問題需要可執行的答案（狀態、商業邏輯、必須親眼看到的 UI），透過原型繞道，用 **`/handoff`** 作為雙向橋樑（見跨 session）：
   - **`/handoff`** 出去，再針對那個檔案開一個新 session，
   - **`/prototype`** 用可拋棄的程式碼回答問題，
   - **`/handoff`** 帶著你學到的東西回來，並從原始想法線程中引用它。
3. **分支——這是多 session 的建置嗎？**
   - **是** → **`/to-prd`**（把這段對話轉成 PRD）→ **`/to-issues`**（把 PRD 拆成可獨立認領的 issues）。由於 issues 是獨立的，**每個 issue 之間清除 context**：每個 issue 開新 session，帶著 PRD 和單一 issue 啟動 **`/implement`**。
   - **否** → 直接在這裡執行 **`/implement`**，在同一個 context 視窗中。

### Context 衛生

把步驟 1–3 保持在**一個不中斷的 context 視窗**中——在 `/to-issues` 完成之前不要壓縮或清除——讓訪談、PRD 和 issues 都建立在同樣的思考基礎上。每個 `/implement` 再從新 session 開始，從 issue 出發。

這個限制是**[智慧區（smart zone）](https://www.aihero.dev/ai-coding-dictionary/smart-zone)**：視窗（頂尖模型約 120k tokens）內模型仍能清晰推理的範圍。如果 session 在 `/to-issues` 之前接近上限，不要在衰退的狀態下硬撐——用 `/handoff` 後在新線程繼續。

## 入口道

產生工作後匯入主流程的起始情況。

- **Bug 和需求堆積** → **`/triage`**。它讓 issues 通過 triage 角色並產出 agent 可處理的 issues，後續再由 **`/implement`** 接手。

  Triage 只用於**你沒有建立的** issues——bug 回報、傳入的功能請求、任何未加工就到來的東西。`/to-issues` 產出的 issues 已是 agent-ready，**不要對它們做 triage**。

## 程式碼庫健康

不是功能工作——是維護。

- **`/improve-codebase-architecture`** — 有空時隨時執行，讓程式碼庫保持對 agent 友善的狀態。它浮出深化機會；選擇一個就**產生一個想法**，可以帶進 `/grill-with-docs` 進入主流程。

## 跨 session

- **`/handoff`** — 當一個線程已滿或你需要分支（例如進入 `/prototype` session），這會把對話壓縮成一個 markdown 檔案。你不是在原地繼續——你**開啟新 session 並引用那個檔案**來跨越 context 視窗。它是 context 視窗之間的橋樑，雙向皆可。當你想要**新 session** 但需要**保留目前對話**時使用。
- **`/compact`**（內建）— 停留在**同一個對話**中，讓較早的回合被摘要。在**階段之間的刻意休息點**使用，當你不介意失去逐字歷史記錄時。不要在階段中途壓縮——agent 可能會迷失方向。`/handoff` 是分叉；`/compact` 是繼續。

## 獨立項目

完全脫離主流程。

- **`/grill-me`** — 與 `/grill-with-docs` 相同的無情訪談，但用於**沒有程式碼庫**時。無狀態：不在本地保存任何東西，不建立 `CONTEXT.md`。用它來磨練任何不在 repo 中的計畫或設計。
- **`/teach`** — 在多個 session 中學習一個概念，以目前目錄作為有狀態的工作區。
- **`/writing-great-skills`** — 寫好與編輯 skill 的參考。

## 前提條件

**`/setup-matt-pocock-skills`** — 在第一次使用 engineering 流程前執行，設定其他 skill 所需的 issue 追蹤工具、triage 標籤和文件格局。

---
name: code-review
description: 審查自某個固定點（commit、branch、tag 或 merge-base）以來的變更，沿著兩個軸線——Standards（程式碼是否遵循本 repo 文件化的程式碼標準？）和 Spec（程式碼是否符合原始 issue/PRD 的要求？）。以平行 sub-agent 執行兩者的審查，並將結果並排呈現。用於使用者想審查某個 branch、PR、進行中的變更，或要求「review since X」時。
---

自 `HEAD` 與使用者提供的固定點之間的 diff，進行兩軸審查：

- **Standards** — 程式碼是否遵循本 repo 文件化的程式碼標準？
- **Spec** — 程式碼是否忠實實作了原始的 issue / PRD / spec？

兩個軸線都以**平行 sub-agent**執行，避免互相污染彼此的 context，然後這個 skill 彙整它們的發現。

issue 追蹤工具應該已經提供給你——如果 `docs/agents/issue-tracker.md` 不存在，執行 `/setup-matt-pocock-skills`。

## 流程

### 1. 釘住固定點

使用者說的就是固定點——一個 commit SHA、branch 名稱、tag、`main`、`HEAD~5` 等等。如果他們沒指定，就詢問。

一次性記下 diff 指令：`git diff <fixed-point>...HEAD`（三個點，所以比較的對象是 merge-base）。也記下 commit 清單：`git log <fixed-point>..HEAD --oneline`。

在繼續之前，先確認固定點可以解析（`git rev-parse <fixed-point>`）且 diff 非空。錯誤的 ref 或空的 diff 應該在這裡就失敗——而不是在兩個平行 sub-agent 裡面才發現。

### 2. 找出 spec 來源

依以下順序尋找原始 spec：

1. commit 訊息中的 issue 引用（`#123`、`Closes #45`、GitLab 的 `!67` 等）——透過 `docs/agents/issue-tracker.md` 中的流程取得。
2. 使用者以參數傳入的路徑。
3. `docs/`、`specs/` 或 `.scratch/` 底下符合 branch 名稱或功能的 PRD/spec 檔案。
4. 如果都找不到，詢問使用者 spec 在哪裡。如果他們說沒有，**Spec** sub-agent 就跳過並回報「沒有可用的 spec」。

### 3. 找出 standards 來源

repo 中任何記載程式碼該怎麼寫的東西，例如 `CODING_STANDARDS.md` 或 `CONTRIBUTING.md`。

除了 repo 文件化的內容之外，Standards 軸線永遠帶著下方的**壞味道基準線（smell baseline）**——一組固定的 Fowler code smell（《Refactoring》第 3 章），即使 repo 什麼都沒文件化也適用。有兩條規則約束它：

- **repo 優先。** 一個文件化的 repo 標準永遠勝出；如果它認可某個基準線本會標記的東西，就壓下那個 smell。
- **永遠是判斷題。** 每個 smell 都是一個帶標籤的啟發式提示（「可能是 Feature Envy」），從來不是硬性違規——而且和這裡的任何標準一樣，跳過任何工具已經強制執行的部分。

每個 smell 讀作*它是什麼* → *怎麼修*；拿它對照 diff：

- **Mysterious Name** — 函式、變數或型別的名稱沒有揭示它做什麼或存放什麼。→ 重新命名；如果想不出誠實的名字，代表設計本身就是模糊的。
- **Duplicated Code** — 同樣的邏輯形狀出現在變更中的一個以上的 hunk 或檔案。→ 抽出共用的形狀，讓兩處都呼叫它。
- **Feature Envy** — 一個方法伸手進另一個物件的資料裡的次數比自己的資料還多。→ 把這個方法搬到它覬覦的資料所在之處。
- **Data Clumps** — 同樣幾個欄位或參數總是一起旅行（一個想要誕生的型別）。→ 把它們綁成一個型別，傳遞那個型別。
- **Primitive Obsession** — 一個原始型別或字串代替了一個值得擁有自己型別的領域概念。→ 給這個概念自己的小型別。
- **Repeated Switches** — 同一個型別上同樣的 `switch`／`if`-cascade 在變更中重複出現。→ 換成多型，或讓兩處共用一個 map。
- **Shotgun Surgery** — 一個邏輯上的變更迫使 diff 中許多檔案出現分散的編輯。→ 把會一起變動的東西收攏進一個模組。
- **Divergent Change** — 一個檔案或模組因為好幾個不相關的理由而被編輯。→ 拆分，讓每個模組只因一個理由變動。
- **Speculative Generality** — 為了 spec 沒有的需求而加入的抽象、參數或掛鉤。→ 刪掉它；內聯回去，直到真正的需求出現。
- **Message Chains** — 呼叫端不該依賴的長串 `a.b().c().d()` 導覽。→ 把這串走訪藏在第一個物件上的一個方法背後。
- **Middle Man** — 一個大多只是把工作往下轉發的類別或函式。→ 砍掉它，直接呼叫真正的目標。
- **Refused Bequest** — 一個子類別或實作者忽略或覆寫了它繼承的大部分東西。→ 拿掉繼承關係，改用組合。

### 4. 平行產生兩個 sub-agent

用單一訊息發出兩個 `Agent` 工具呼叫。兩者都使用 `general-purpose` sub-agent。

**Standards sub-agent 提示** — 包含：

- 完整的 diff 指令與 commit 清單。
- 你在第 3 步找到的 standards 來源檔案清單，**加上第 3 步的壞味道基準線全文貼上**——sub-agent 沒有其他方式取得它。
- 任務說明：「回報——依檔案／hunk 分別列出（如果相關的話）——(a) diff 違反文件化標準的每個地方：引用該標準（檔案 + 規則）；以及 (b) 你發現的任何基準線 smell：命名它並引用該 hunk。區分硬性違規和判斷題——文件化標準的違反可以是硬性的，但基準線 smell 永遠是判斷題，而且文件化的 repo 標準會凌駕於基準線之上。跳過任何工具已強制執行的部分。400 字以內。」

**Spec sub-agent 提示** — 包含：

- diff 指令與 commit 清單。
- spec 的路徑或已取得的內容。
- 任務說明：「回報：(a) spec 要求但缺漏或只完成一部分的需求；(b) diff 中沒有被要求的行為（範圍蔓延）；(c) 看起來已實作但實作方式看起來有問題的需求。針對每個發現引用 spec 的那一行。400 字以內。」

如果找不到 spec，跳過 Spec sub-agent，並在最終報告中註明。

### 5. 彙整

在 `## Standards` 和 `## Spec` 標題下呈現這兩份報告，逐字呈現或稍作整理。**不要**合併或重新排序發現——這兩個軸線是刻意分開的（見*為什麼是兩個軸線*）。

最後以一行摘要收尾：每個軸線的發現總數，以及*各軸線內部*最嚴重的問題（如果有的話）。不要跨軸線挑出單一贏家——那正是分開兩軸要防止的重新排序。

## 為什麼是兩個軸線

一個變更可能通過一個軸線卻在另一個軸線失敗：

- 遵循每一條標準但實作了錯誤東西的程式碼 → **Standards 過，Spec 不過。**
- 完全做到 issue 要求但打破了專案慣例的程式碼 → **Spec 過，Standards 不過。**

分開回報能防止一個軸線掩蓋另一個軸線。

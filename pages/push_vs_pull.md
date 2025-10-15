---
layout: center
transition: slide-left
---

# Push vs Pull
---

### 📖 前情提要

我們這裡要釐清：
> **Push-based** 與 **Pull-based** 這兩種模式，  
> 在細顆粒度（fine-grained） reactivity 裡有什麼不同？

---

### 🧩 核心概念

<v-clicks>

- **Push-based**：把「計算」提早到寫入當下完成。  
- **Pull-based**：把「計算」延遲到真正有人讀取才進行。  
- 換句話說：  
  - Push → 「資料一改，立即通知」  
  - Pull → 「先標髒，等要用時才算」

</v-clicks>

---

### 🍱 生活案例：Push-based

<v-clicks>

- 百貨公司美食街點餐 → 拿到震動圓盤  
- **寫入（下單）**：點餐完成  
- **推送（完成即通知）**：餐做好 → 圓盤震動  
- **副作用（取餐）**：收到訊號就去櫃台領餐  

🧠 對應 reactivity：
> 寫入時就沿依賴鏈重算並通知後續節點。

</v-clicks>

---

### 🧋 生活案例：Pull-based

<v-clicks>

- 手搖飲店點單 → 拿到號碼牌  
- **寫入（下單）**：點餐完成  
- **標記（狀態改變）**：店家掛號到螢幕  
- **讀取 → 重算（查看才發生）**：看到號碼才去拿  
- **副作用（取餐）**：抬頭看到才動作  

🧠 對應 reactivity：
> 寫入只標記 dirty，真正計算延後到被讀取時。

</v-clicks>

---

### 🔍 核心定義對照

| 模式 | 行為焦點 | 簡化流程 |
|------|-----------|-----------|
| **Push-based** | 寫入即計算：資料一改動就立刻 propagate | `set() → propagate → compute → effect` |
| **Pull-based** | 寫入只標記：等讀取時再計算 | `set() → markDirty ⏸ read() → compute → effect` |

> ☑️ 兩者都會「推送」訊號：  
> Push 推的是 **計算**，Pull 推的是 **標記**。

---

### 🧭 時序流程

<v-clicks>

**Push-based**
<!-- ![Push-based](https://ithelp.ithome.com.tw/upload/images/20250805/20129020sTny6WN8x3.png) -->
<img src="https://ithelp.ithome.com.tw/upload/images/20250805/20129020sTny6WN8x3.png" class="mx-auto w-[80%] h-[90%]"/>

</v-clicks>
---

### 🧭 時序流程
<v-clicks>

**Pull-based**
<!-- ![Pull-based](https://ithelp.ithome.com.tw/upload/images/20250805/20129020THYHE5H0Jx.png) -->
<img src="https://ithelp.ithome.com.tw/upload/images/20250805/20129020THYHE5H0Jx.png" class="mx-auto w-[60%] h-[90%]"/>

</v-clicks>

---

### ⚖️ 優缺點比較

| 面向 | Push-based | Pull-based |
|------|-------------|-------------|
| 讀取延遲 | ✅ 最低，資料永遠最新 | ⏳ 若節點髒，第一次讀取需重算 |
| 寫入成本 | 💸 潛在大（依賴深度 × 次數） | 💡 小（僅標記 dirty） |
| 過度計算 | 多，即使結果沒被用到 | 少，只在實際讀取時發生 |
| 批次化能力 | 較難（先算完） | ✅ 天生適合：可 flush 一次性更新 |
| 除錯可觀測性 | 依賴鏈展開明確 | 需 DevTools 追蹤 pull 時機 |
| 適合場景 | 高寫入、低讀取（協同編輯） | 低寫入、高讀取（儀表板） |

---

### 🧩 應用場景與建議策略

| 典型需求 | 建議模式 | 說明 |
|-----------|-----------|------|
| 即時共同編輯、遊戲同步 | **Push** | 每次寫入即時反映、依賴鏈淺 |
| 監控面板、資料視覺化 | **Pull** | 寫入少但讀取頻繁 |
| 動畫、Timeline、Scroll 驅動 | **Pull + Scheduler** | 延遲重算、批次更新 frame |
| 資料 Pipeline、一次性運算 | **Push-on-Commit** | 一次算完再分發重用結果 |

> React 屬於 **Pull + Scheduler**（靠 batching update）。  
> RxJS、MobX 則偏向 **Push-on-Commit**。

---

### ❓ 常見迷思澄清

<v-clicks>

- **Pull 就一定全域掃描？**  
  → ❌ 不必，只在讀取時檢查該鏈的 dirty 標記。  

- **Push 必定浪費計算？**  
  → ❌ 當更新後立刻被使用，先算反而更快（例如協作游標）。  

- **Push 與 Pull 只能二選一？**  
  → ❌ 現代 signal framework 多採 **Hybrid push-pull**：  
    - 寫入階段 push 髒標記  
    - 讀取階段 pull 重算（兼具即時性與惰性計算）

</v-clicks>

---

### 🔍 為什麼即使是 fine-grained 系統，也仍需要 Pull？

<v-clicks>

- 因為我們**無法預測「會不會被用到」與「何時用到」**。  
- Pull 讓「資料變了」與「要不要算」分離：  
  - 寫入階段只標 dirty  
  - 讀取階段再決定是否重算

</v-clicks>

---

### 🧠 結語：Push vs Pull 的意義

<v-clicks>

- 在 coarse-grained（VDOM）世界，整棵樹 diff 已足夠抽象。  
- 但在 fine-grained 世界，一次 `set()` 可能波及數百個 derivation。  
- 此時，「**計算發生的時機**」決定：
  - 整體性能瓶頸  
  - 體感延遲與互動流暢度  
  - Scheduler 設計與任務切片方式

</v-clicks>

<v-click>

> 🔑 理解 Push 與 Pull 的本質，  
> 你就擁有評估每個框架 reactivity 解法的底圖。

</v-click>

<v-click>

> 下一章，我們將看主流框架如何融合兩者的優勢。
</v-click>

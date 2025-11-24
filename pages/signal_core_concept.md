---
layout: center
transition: slide-left
---

# Signal 運作原理：資料 → 衍生 → 副作用

---
layout: two-cols
transition: slide-left
---

### 🧩 核心流程：資料 → 衍生 → 副作用

<!-- ![核心流程](https://ithelp.ithome.com.tw/upload/images/20250806/201290201ITMntjbpi.png){class="w-[75%] mx-auto"} -->
<img class="w-[60%] h-[85%] mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/201290201ITMntjbpi.png">

::right::

<v-clicks class="pt-8">

- 在 fine-grained reactivity 裡，  
  state 被拆成許多最小的 **Signal**。
- 由它們推導出的衍生值稱為 **Computed**。
- 與外界互動（DOM / 網路 / console）的邏輯放在 **Effect**。

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### 📦 Signal：最小的「狀態容器」

<img class="w-[90%] h-[60%] mt-4 mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020moMcickoEs.png">

::right::

<v-clicks class="pt-8">

- **讀取值**：可隨時取出當前值。  
- **修改值**：可更新內容。  
- **自動通知依賴者**：當值變化時，通知所有觀察者。

🧠 一個 signal = 一個可追蹤的「值單元」。

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### ⚙️ Signal 的實作流程

<!-- ![Signal 流程](https://ithelp.ithome.com.tw/upload/images/20250806/20129020u1frfN4Spv.png){class="w-[70%] mx-auto"} -->
<img class="w-[90%] mt-4 mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020u1frfN4Spv.png">

::right::

<v-clicks class="pt-8">

- 儲存可變值並維護一組 **observers**。  
- 讀取時 → 記錄「目前誰在觀察」。  
- 寫入時 → 更新值並標記所有 observers 為 dirty。  
- 真正的重算交給 **Scheduler**。

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### 🧮 Computed：惰性快取的衍生值

<!-- ![Computed 圖](https://ithelp.ithome.com.tw/upload/images/20250806/20129020zZqaUl4vkq.png){class="w-[70%] mx-auto"} -->
<img class="w-[90%] mt-4 mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020zZqaUl4vkq.png">

::right::

<v-clicks class="pt-8">

- **惰性 + 快取**：  
  只有在第一次呼叫或依賴變髒時才重算。  
- **角色雙重性**：  
  同時是 observer（觀察 signal），也是被觀察者。  
- **必須純函式**：  
  所有副作用留給 Effect 處理。

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### ⚡ Effect：副作用的執行者

<!-- ![Effect 圖](https://ithelp.ithome.com.tw/upload/images/20250806/20129020TzdL2M5JLF.png){class="w-[70%] mx-auto"} -->
<img class="w-[90%] mt-4 mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020TzdL2M5JLF.png">

::right::

<v-clicks class="pt-8">

- 負責「與外界互動」的行為。  
- 會訂閱對應的 Signals / Computeds。  
- **真正觸發時機**由 Scheduler 控制。  
- 執行純副作用，不回傳值。

</v-clicks>

---

### 🕹️ Scheduler：批次與優先權的守門員

<!-- ![Scheduler 流程](https://ithelp.ithome.com.tw/upload/images/20250806/20129020kh9WF8xNiJ.png){class="w-[75%] mx-auto"} -->
<img class="w-[90%] mt-4 mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020kh9WF8xNiJ.png">

<v-clicks class="pt-8">

- 接手所有被標為 dirty 的節點。  
- 在下一個微任務（microtask）中一次性處理：
  1. 先重算 Computed（確保資料正確）  
  2. 再觸發 Effect（執行副作用）
- 常見實作：`queueMicrotask` 或 `requestAnimationFrame`。  
- 目標：**合併多次變動成一次重算**。

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### 🔄 Flow Chart：完整運作全貌

<!-- ![Flow chart](https://ithelp.ithome.com.tw/upload/images/20250806/20129020XmFp5wDe8V.png){class="w-[75%] mx-auto"} -->
<img class="w-[60%] h-[80%] mx-auto" src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020XmFp5wDe8V.png">

::right::

<v-clicks class="pt-8">

1️⃣ Signal 值改變 → 標記依賴 dirty  
2️⃣ Scheduler 收集 dirty 節點  
3️⃣ Computed 先重算  
4️⃣ Effect 最後執行副作用  

🧠 **資料流：Write → Schedule → Compute → Commit**

</v-clicks>

---
layout: center
---
### 🧠 總整理

<v-clicks>

- **Signal**：最小、可追蹤的 state 單元。  
- **Computed**：惰性快取的純函式衍生值。  
- **Effect**：與外界互動的副作用邊界。  
- **Scheduler**：負責批次重算與排程。  

🧩 四者組合起來，就是 **Fine-grained Reactivity** 的骨幹。  
每次資料改變，都能以最小成本更新正確的節點。

</v-clicks>

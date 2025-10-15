---
layout: center
transition: slide-left
---

# 承先啟後的發展：Reactivity 的進化史
---

### 🧩 2010：Knockout.js 的誕生

- 首度將 **Observable / Computed** 帶進前端世界  
- 讓「資料自己開口、UI 跟着動」成為可行路徑  
- 奠定了後續 Angular / React / Vue 各自不同的思考方向

<v-click>

> Knockout 讓大家第一次看到：
>  
> 👉 *資料 → 視圖* 可以自動同步，而不是人為改 DOM。

</v-click>

---

### ⚙️ Reactivity 的核心概念

<v-clicks>

- **聲明式**：你描述「要呈現什麼」，框架決定「怎麼反映變化」
- **依賴追蹤**：第一次讀資料時，自動記下「誰用到誰」
- **變動傳播**：資料變化後，沿依賴圖傳遞失效訊號到對應 UI
- 目的：  
  - 減少心智負擔（不需手動同步）  
  - 效能最佳化（只改變必要部分）  
  - 資料流向清楚（更易除錯與測試）

</v-clicks>

---

### 🔄 兩大策略：誰先開口？

| 策略 | 代表作法 | 關鍵詞 |
|------|-----------|--------|
| **Pull**（框架主動「打聽」） | 迴圈比對 / Virtual DOM diff | dirty-checking, diff |
| **Push**（資料主動「通知」） | watcher / signal | observable, effect |

> 多數現代框架其實是 Hybrid：  
> 由資料端 push「失效」，再於適當時機 pull「重算」。

---

### 🧭 四大模型的演進光譜

![四大模型光譜](https://ithelp.ithome.com.tw/upload/images/20250902/20129020vQ3OSK755H.png)

<v-clicks>

- 每一代都在回應同一個問題：  
  「**要多久發現變化？要改動多大範圍？**」
- 從全域掃描 → 子樹比對 → 精準追蹤 → 編譯期優化

</v-clicks>

---

### 📚 四大模型對照表

| 模型 | 核心流程 | Push/Pull | 粒度 & 備註 | 代表框架 |
|------|-----------|------------|---------------|-----------|
| Dirty-checking | `$digest` 比對所有 `$watch` | 純 Pull | 元件/表達式層級；效能隨監聽數量線性衰減 | AngularJS 1.x |
| Virtual DOM diff | setState Push 髒標記 → Pull diff → Patch | Hybrid | 子樹為單位；心智簡單但易過度 render | React / Vue 2 |
| Watcher / Observable Graph | setter Push 通知相關 Watcher | 偏 Push | getter 追蹤依賴；粒度更細 | Vue 2 Watcher, MobX |
| Fine-grained Signals | setter Push → lazy Pull → DOM patch | Hybrid 或近純 Push | 屬性/DOM 級；依賴圖最細 | Solid.js, Angular Signals, Svelte 5 Runes |

---

### 🧩 模型視覺化流程對照

<v-clicks>

- Dirty-checking  
  ![Dirty-checking](https://ithelp.ithome.com.tw/upload/images/20250902/20129020jaT1gNL2Kb.png)

- Virtual DOM diff  
  ![VDOM diff](https://ithelp.ithome.com.tw/upload/images/20250902/20129020EyuhcMXbG4.png)

- Watcher / Observable Graph  
  ![Watcher Graph](https://ithelp.ithome.com.tw/upload/images/20250902/20129020Dv1KT4vrnV.png)

- Fine-grained Signals  
  ![Fine-grained](https://ithelp.ithome.com.tw/upload/images/20250902/20129020iZIiWIcr7x.png)

</v-clicks>

---

### 🔍 深入比較重點

<v-clicks>

1️⃣ **Pull vs. Push — 誰先開口？**
- Dirty-checking：純 Pull（框架主動掃資料）
- Virtual DOM：Push 髒標記 → Pull diff（Hybrid）
- Watcher／Signals：Push 失效 → Lazy Pull（Hybrid）
- Compile-time signals：依賴編譯期決定，近純 Push

2️⃣ **依賴精度 — 通知多細？**
- VDOM：知道哪棵 component 樹變
- Signals：知道哪個 Memo／DOM 節點變
- Compile-time：直接輸出 DOM 操作，最細緻

</v-clicks>

---

### 🔍 深入比較重點

<v-clicks>

3️⃣ **調度（Scheduling）— 何時動手？**
- React / Solid：microtask 批次合併
- Vue 3：job-queue 分批
- Dirty-checking：同步跑迴圈（成本高）

4️⃣ **心智模型 — 寫程式的感受**
- Signals / MobX：像直接改值 → 框架自動同步
- Virtual DOM：Render ≠ Paint 的宣告式心智
- Compile-time signals：更直覺，靠編譯器保障一致性

</v-clicks>

---

### 🧠 推與拉的歷史

<v-clicks>

- Reactivity 的進化，其實是一條不斷調和「Push」與「Pull」的旅程。  
- 我們從 Dirty-checking 的純 Pull，  
  → 到 Virtual DOM 的 Hybrid，  
  → 再到 fine-grained 的「圖形化依賴」。  
- Runtime signals 採「Push 失效 + Lazy Pull」策略，  
  Compile-time signals 則前移到編譯期，幾乎純 Push。

</v-clicks>

<v-click>

> 即使在最細顆粒的系統裡，我們仍然需要 Pull。  
> 因為真正的難題不只是「通知」，而是「何時該重算」。

</v-click>

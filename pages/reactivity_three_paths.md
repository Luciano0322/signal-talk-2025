---
layout: center
transition: slide-left
---

# Reactivity 三路線：Signal、Proxy、Virtual DOM
---
layout: center
---
### 💡 問題意識
<p>
我們常在 Signal / Proxy / Virtual DOM 之間搖擺：  
</p>
<p class="mt-4 border-l-4 border-[#00e6b8] border-solid pl-4">
哪個更快？哪個更好維護？
</p>

<v-click>

如果我們回到那句經典口號：
### UI = f(state)  

</v-click>

---

### 📜 「UI = f(state)」小故事

<!-- ![UI = f(state)](https://ithelp.ithome.com.tw/upload/images/20250905/20129020201JcSyAeu.png){class="w-[70%] mx-auto"} -->
<img src="https://ithelp.ithome.com.tw/upload/images/20250905/20129020201JcSyAeu.png" class="w-[80%] mx-auto" />

<v-clicks>

- **1997 — Conal Elliott** 在 ICFP 發表 *Functional Reactive Animation*：   
  主張畫面是「時間 → 圖形」的純函數。  
- **2012 — Evan Czaplicki** 發表 Elm：`view : Model → Html msg`  
  第一次讓前端看見 *UI = f(state)* 的實作。
- **2013 — Jordan Walke** 把概念帶入 React，  
  口訣寫在投影片上：「UI = f(state)」。

</v-clicks>

<v-click>

> 這句話讓函數式研究走出論文，  
> 成為前端工程師的共同語言。

</v-click>

---
layout: center
---

### 🧭 三條演化路線

<v-clicks>

1️⃣ **Signal**：把「該不該重算」綁在值上。  
2️⃣ **Proxy Reactive**：用語言特性自動化依賴追蹤。  
3️⃣ **Virtual DOM**：用比對（Diff）換取無侵入的 DX。

</v-clicks>

---
layout: center
---

### 🧩 Signal：把「更新」包在值裡

<v-clicks>

**1. 依賴追蹤**  
- 讀取 signal 時收集依賴；寫入時只通知「真的用到它」的節點。

**2. 觸發與調度**  
- 以 push 為主，可 batch 或 lazy evaluate（Hybrid）。

**3. DOM 更新**  
- 直達節點／屬性，不需整棵 diff。

**適合場景**  
- 高頻微互動（滑桿、地圖標記、Canvas 等）。

</v-clicks>

---
layout: center
---

### 🧠 Proxy Reactive：用語言特性自動化依賴

<v-clicks>

**1. 依賴追蹤**  
- 利用 Proxy 攔截 get/set，自動建立屬性級依賴。  

**2. 觸發與調度**  
- push 模式，寫入時觸發相依計算，  
  通常以微任務 job queue 批次更新（Vue）。  

**3. DOM 更新**  
- 根據 getter 追蹤結果決定 patch 範圍，  
  深層結構讀取時有額外成本。

**適合場景**  
- 保留「直接改物件」手感的表單、巢狀資料結構。

</v-clicks>

---
layout: center
---

### ⚙️ Virtual DOM：用「比對」換取「無侵入」

<v-clicks>

**1. 依賴追蹤**  
- 幾乎不追蹤屬性，只把 render 結果視作快照。  

**2. 觸發與調度**  
- `setState` 觸發 component 重跑，  
  React 以 Fiber 排程管理優先級與切片。

**3. DOM 更新**  
- Diff 產生 patch；小變動仍需重跑 component。

**適合場景**  
- 深耕 React 生態、SSR / RSC 導向專案。

</v-clicks>

---

### 🧪 三者的最小對照程式碼


**Solid.js (Signal)**

```ts
const [count, setCount] = createSignal(0)
createEffect(() => console.log('count ->', count()))
setCount(1) // 只觸發用到 count 的 effect
```

**Vue (Proxy Reactive)**

```ts
const state = reactive({ count: 0 })
watchEffect(() => console.log('count ->', state.count))
state.count++ // 深層 getter 會註冊依賴
```

**React (Virtual DOM)**

```tsx
function Counter() {
  const [count, setCount] = useState(0)
  console.log('render')
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}
```

<v-click>

- Signal / Proxy：由「資料」決定是否重算。
- Virtual DOM：由「比對結果」決定是否更新。

</v-click>

---

### ⚖️ 三者核心差異對照表

| 面向     | Signal              | Proxy Reactive    | Virtual DOM     |
| ------ | ------------------- | ----------------- | --------------- |
| 依賴追蹤   | 讀時收集，值級             | get/set 攔截，屬性級    | 幾乎不追蹤           |
| 觸發模式   | push 主導 + lazy pull | push + job queue  | pull + diff     |
| DOM 更新 | 直達節點                | 依 getter 決定範圍     | 子樹 diff         |
| 調度策略   | microtask / batch   | job queue / flush | Fiber scheduler |
| 適合場景   | 高頻微互動               | 巢狀資料結構            | 通用 UI、生態豐富      |

---

### 📈 選型建議表

| 需求情境             | 建議技術                | 原因                |
| ---------------- | ------------------- | ----------------- |
| 遊戲座標、Canvas 畫筆   | **Signal**          | micro-update 成本最低 |
| 巨型表單、深層 JSON 編輯器 | **Proxy Reactive**  | 語法自然，依賴自動收集       |
| React 生態、多人協作專案  | **Virtual DOM**     | DX、工具鏈與社群優勢       |
| 局部熱點需極速          | **React + Signals** | 混合策略，熱點區域細化更新     |

---
layout: center
---
### 小結
<v-clicks>

- Signal：把「要更新哪裡」包在值本身。
- Proxy Reactive：用 ES6 Proxy 自動讓屬性可追蹤。
- Virtual DOM：不問過程，只比「前後結果」。

理解三者在「依賴 → 調度 → DOM」的成本分佈，  
你就能根據場景，選擇最貼切的 reactivity 策略。

</v-clicks>
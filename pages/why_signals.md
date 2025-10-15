---
layout: center
transition: slide-left
mdc: true
---

# 為何需要 Signal？
## 把「重算」縮到資料最小單元
---
layout: two-cols
transition: slide-left
---

### React 如何處理 state（回顧）
<img src="https://ithelp.ithome.com.tw/upload/images/20250812/20129020vIIaX2bGN7.png" class="w-[60%] h-[80%] mx-auto rounded shadow" />

::right::

<v-clicks class="pt-8">

- 事件 → `setState` → 重新執行元件 function → 產生新 VDOM → Diff → Commit
- 流程熟悉、心智一致，但**函式重跑**與**子樹比對**是基礎成本

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### Fine-grained Reactivity 的處理方式
<img src="https://ithelp.ithome.com.tw/upload/images/20250806/20129020dD0Lsnk1Ee.png" class="w-[75%] h-[90%] mx-auto rounded shadow" />

::right::

<v-clicks class="pt-8">

- 讀時建立依賴；寫時**精準通知**受影響節點
- Scheduler 批次：先重算 Computed，再觸發 Effect；**直達 DOM 節點**

</v-clicks>

---

### 核心差異一覽（直覺版）

| 面向 | React useState & VDOM | Fine-Grained（Signal/Atom） |
|---|---|---|
| **更新單位** | Component（整棵子樹） | State cell（單一值） |
| **依賴追蹤** | 每次 render 重跑 → 比對 VDOM | 讀取即註冊；寫入精準通知 |
| **排程模型** | 異步批次 + Diff + Commit | 異步批次（batch）+ 拓撲排序 + 直達副作用 |
| **閒置成本** | UI 不變也要重跑/生成 VDOM | 結果不變即短路，不進下游 |
| **心智模型** | UI = f(state) | state = f(source)；UI 是 Effect |
| **最佳化** | memo/useMemo/useCallback | 多數內建；跨層才顯式 memo |
| **DevTools** | 成熟 | 起步（Solid/Vue/MobX 等） |

<v-click>

> **要點**：Signal 把「重渲染邊界」從 Component 級縮到 **State 級**，複雜度隨規模**線性**而非**指數**成長。

</v-click>

---

### 為什麼用 useState 會「多做功」？

<v-clicks>

- **函數重跑開銷**：為取得新 VDOM，整個元件函式重跑；大型樹常見「渲染但畫面不變」。
- **元件邊界 ≠ 資料邊界**：多個 state＋layout 綁在同一元件，任一 state 改動都牽動整個函式。

</v-clicks>

---

### Signal 的術語與 React 類比

| 術語 | React 類比 | 功能 |
|---|---|---|
| **Source / Signal** | `useState` 的 state 變數 | 最原始、可寫入的資料節點 |
| **Computed / Derivation** | `useMemo` | 由 Signal 衍生、具快取的純函式 |
| **Effect / Reaction** | `useEffect` | 依賴變化後執行副作用 |
| **Batch / Transaction** | `unstable_batchedUpdates` | 將多次寫入壓縮為一次傳播 |
| **Graph / Dependency Map** | React Fiber（比喻） | 追蹤資料依賴的有向圖 |

---

### Signal 的心智圖
<img src="https://ithelp.ithome.com.tw/upload/images/20250807/201290200xRWqSShC7.png" class="w-[60%] h-full mx-auto mt-3 rounded" />

---

### Fine-grained 三步驟：讀 → 寫 → 傳

| 階段 | 示意程式碼 | 內部動作 |
|---|---|---|
| **讀（Tracking）** | `console.log(count())` | 記錄「目前執行的 Computed/Effect 依賴 count」 |
| **寫（Mark Dirty）** | `count.set(v => v + 1)` | 只做兩件事：①更新值 ②把受影響節點標 dirty 入佇列 |
| **傳（Propagate）** | （Scheduler flush） | 拓撲排序：先重算 Computed → 值有變才往下游 Effect |

<v-click>

**短路示例**：`Math.floor(a/10)` 值未變 → **整條支鏈不觸發**，成本 ≈ 受影響節點數 × O(1)

</v-click>

---

### 對照範例：Counter

**React**
```tsx {all|4|2|4-7|all}
function Counter() {
  const [count, setCount] = useState(0)
  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count:
      <span>{count}</span>
    </button>
  )
}
// 點擊 → 元件重跑 → 產生新 VDOM → Diff <span> → Commit
```

**Fine-grained (Solid.js)**
```tsx {all|3|1|5|all}
const [count, setCount] = createSignal(0)
const Counter = () => (
  <button onClick={() => setCount(c => c + 1)}>
    Count:
    <span>{count()}</span>
  </button>
)
// 點擊只觸發文字節點內容；無函式重跑、無整棵 diff
```

---

### Fine-grained 的四個優勢
<v-clicks>

1. **更新成本與 UI 大小解耦**：只重算受影響的 Computed/Effect；大列表/白板/表格與資料單元數線性關係。   
2. **預測式資料流**：依賴圖可走訪 → 變動追蹤、why-did-you-update 變簡單。  
3. **副作用分離**：`effect(() => domRef(), data())` 僅在依賴值真的改變時觸發，避開 deps 陣列陷阱。  
4. **測試更輕量**：Computed 為純函式 → 可在 Node 跑，不必拉 DOM / renderer。  

</v-clicks>

---

### 小結：Fine-grained 的核心價值
<v-clicks> 

- **Signal**：最小可觀測單元，把「要不要重算」綁在值上  
- **Computed / Effect**：把邏輯與副作用分離，讓 UI 更新變得可預測  
- **Scheduler**：批次協調更新順序，確保一致性與效能  

</v-clicks>
<v-click> 

> **Fine-grained Reactivity**  
> 不再以「元件」為界，而是以「資料」為界。  
> 每一次變動，都是**可被追蹤、可被推導**的結果。

</v-click>

---

### ⏱️ 接下來的問題：**什麼時候該執行？**

<v-clicks>

- 我們已經知道「要改誰」——依賴追蹤幫我們決定了更新範圍。  
- 但還有另一個問題：**這些更新該在什麼時候執行？**
- React 用 **Fiber Scheduler** 負責這件事；  
  Fine-grained 系統也有自己的 **Scheduler**，但邏輯完全不同。

</v-clicks>

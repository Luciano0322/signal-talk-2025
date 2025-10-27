---
layout: center
class: text-center
---

# React Fiber vs Signal Scheduler

---

| 面向 | React Fiber | Signal Scheduler |
|------|--------------|------------------|
| 負責的層級 | UI 任務排程 | 資料變動排程 |
| 單位 | Component | Reactive node |
| 時間觀 | Render / Commit | Track / Flush |
| 目標 | 流暢的繪製體驗 | 精準的資料一致性 |

---
layout: center
class: text-center
---

<v-clicks>


- React 管「畫面要怎麼畫」  
- Signal 管「資料何時變」

</v-clicks>

<v-click>

<p class="mt-4 border-l-4 border-[#00e6b8] border-solid pl-4">Signal Scheduler = 資料層的 Fiber。</p>

</v-click>

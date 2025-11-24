---
layout: center
transition: slide-left
---

# Push vs Pull

---
layout: two-cols
transition: slide-left
---
### 生活案例：Pull-based
早餐店點餐為例

```mermaid
sequenceDiagram
    participant Customer as 客人
    participant Shop as 早餐店
    
    Customer->>Shop: 阿姨我的好了嗎？
    Shop-->>Customer: 立刻查看並回覆（同步）
    
    Note over Customer,Shop: 客人: 主動查詢 / 阿姨: 被動、同步回應
```

::right::

<v-clicks class="pt-8">
 
- **提供者（阿姨）**：被動方   
- **取用者（帥哥/美女）**：主動方  
- **動作（主動問）**：同步回應  

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### 生活案例：Push-based
賣場排隊結帳為例

```mermaid
sequenceDiagram
    participant Shop as 店員
    participant Customer as 客人
    
    Customer->>Shop: 排隊等待（被動）
    Shop-->>Customer: 下一位！（推播通知）
    
    Note over Shop,Customer: 店員主動通知 → 客人要立即反應
```

::right::

<v-clicks class="pt-8">

- **提供者（店員）**：主動方  
- **取用者（客人）**：被動方  
- **動作（下面一位）**：同步 / 非同步 (店員換班)  

</v-clicks>

---
layout: two-cols
transition: slide-left
---

### 生活案例：Push-Pull
買手搖飲料
```mermaid
sequenceDiagram
    participant Customer as 客人
    participant Shop as 店家
    participant Screen as 出餐螢幕
    
    loop 客人每隔一段時間檢查螢幕
        Customer->>Screen: 看自己的號碼到了沒（Pull）
        Screen-->>Customer: 顯示目前出餐號碼（同步）
    end
    
    Shop-->>Screen: 更新出餐號碼（Push）
    Shop-->>Customer: 87號！請取餐（Push）
    
    Note over Customer,Screen: Push 叫號 + Pull 查看 → 雙向更新最穩定
```

::right::

<v-clicks class="pt-8">

- **提供者（店員）**：主動方  
- **取用者（客人）**：主動方  
- **動作（主動問 / 主動通知）**：同步 / 非同步  

</v-clicks>

<v-click>
  <p class="mt-4 border-l-4 border-[#00e6b8] border-solid pl-4">
  Signals runtime 就是這個模式 
  </p>
  <p class="mt-4 border-l-4 border-[#00e6b8] border-solid pl-4">
  Push 建立資料變動 → Pull 批次 flush effect → 負載平衡、更新精準。
  </p>
</v-click>

# VS_Code_Extension_JSON_format_v3 巨集腳本流程圖

以下為該巨集的【主要流程圖】以及各函數的【處理邏輯細部流程圖】（以文字流程圖說明，適合轉為繪圖工具繪製）。

---

## 1. 主程序流程圖 (formatJson)

```mermaid
flowchart TD
  Start([開始])
  Init[初始化變數 indentSize, indentChar, newLine]
  GetContent[funcContent 取得檔案內容]
  ParseJSON[parseJson 解析 JSON 字串]
  FormatObj[formatJsonObject 格式化 JSON 物件]
  SelectAll["document.selection.SelectAll()"]
  ReplaceText["document.selection.Text = formattedText"]
  End([結束])
  
  Start --> Init
  Init --> GetContent
  GetContent --> ParseJSON
  ParseJSON --> FormatObj
  FormatObj --> SelectAll
  SelectAll --> ReplaceText
  ReplaceText --> End
```

  Start --> Init --> GetContent --> ParseJSON --> FormatObj --> SelectAll --> ReplaceText --> End

---

## 2. funcContent 取得檔案內容

```mermaid
flowchart TD
  A1[開始]
  A2[content用document.GetLine函數取得第1行]
  A3{for i = 2 到 總行數}
  A4[document.GetLine第 i 行內容與換行字元加到 content]
  A5[回傳 content]
  A1 --> A2 --> A3
  A3 -- 是 --> A4 --> A3
  A3 -- 否 --> A5
```

---

## 3. parseJson 解析 JSON 字串

```mermaid
flowchart TD
  B1[開始]
  B2[移除前後空白和 BOM]
  B3[使用 new Function 解析 jsonString，取得 jsonObj]
  B4[回傳 jsonObj]
  B1 --> B2 --> B3 --> B4
```

---

## 4. formatJsonObject 格式化 JSON 物件 (核心遞迴)

```mermaid
flowchart TD
  C1[C1<br>開始]
  C2[C2<br>取得縮排字串]
  C3{C3<br>obj 是 null?}
  C4[C4<br>回傳 null]
  C5{C5<br>obj typeof?}
  C6[C6<br>undefined]
  C7[C7<br>number/boolean]
  C8[C8<br>string]
  C9[C9<br>object]
  C10{C10<br>isArray（obj）?}
  C11[C11<br>處理陣列]
  C12[C12<br>處理物件]
  C13[C13<br>回傳 result]
  C14[C14<br>回傳 String（obj）]

  C1 --> C2 --> C3
  C3 -- 是 --> C4
  C3 -- 否 --> C5
  C5 -- undefined --> C6 --> C14
  C5 -- number/boolean --> C7 --> C14
  C5 -- string --> C8 --> C14
  C5 -- object --> C9
  C9 --> C10
  C10 -- 是 --> C11
  C10 -- 否 --> C12
  C11 --> C13
  C12 --> C13
  C13 --> C14
```

#### （補充說明：）
- **C11 處理陣列**
  - 若為空陣列，回傳 []
  - 否則：for loop 遞迴 formatJsonObject，逗號分隔，適當加縮排和換行
- **C12 處理物件**
  - 計算屬性數量，若為空物件，回傳 {}
  - 否則：for loop 遞迴 formatJsonObject，逗號分隔，適當加縮排和換行

---

## 5. getIndent 取得縮排

```mermaid
flowchart TD
  D1[開始]
  D2[for i = 0 到 level*indentSize]
  D3[result += indentChar]
  D4[回傳 result]
  D1 --> D2
  D2 --> D3 --> D2
  D2 --否--> D4
```

---

## 6. isArray 檢查是否為陣列

```mermaid
flowchart TD
  E1[開始]
  E2[判斷 obj 是否為陣列]
  E3[回傳 Boolean 結果]
  E1 --> E2 --> E3
```

---

## 7. getArrayLength 取得陣列長度

```mermaid
flowchart TD
  F1[開始]
  F2[length = 0]
  F3{arr［i］有值嗎}
  F4[length++]
  F5[回傳 length]
  F1 --> F2
  F2 --> F3
  F3 -- 是 --> F4 --> F3
  F3 -- 否 --> F5
```
---

## 8. escapeString 轉義字串

```mermaid
flowchart TD
  G1[開始]
  G2[for i = 0 到 str.length]
  G3[取得str.charAt（i）字元]
  G4{判斷是否為特殊字元}
  G5[遇到特殊字元處理為 escape]
  G6{ASCII < 32 或 > 126?}
  G7[unicode escape]
  G8[直接加字元]
  G9[回傳 result]
  G1 --> G2
  G2 --> G3 --> G4
  G4 -- 特殊字元 --> G5 --> G2
  G4 -- 其他 --> G6
  G6 -- 是 --> G7 --> G2
  G6 -- 否 --> G8 --> G2
  G2 -- 結束 --> G9
```

---

## 9. formatJson 執行流程

```mermaid
flowchart LR
  A[funcContent<br>讀取內容]
  B[parseJson<br>解析JSON]
  C[formatJsonObject<br>遞迴格式化]
  D[覆蓋文件內容]

  A --> B --> C --> D

  subgraph 標題
  direction TB
  T[9. formatJson 執行流程]
  end
  style T fill:#f9f,stroke:#333,stroke-width:4px
```

---

### 補充說明

- **主要邏輯遞迴在 formatJsonObject**，根據類型判斷與格式化。
- **escapeString 處理所有字串輸出安全**。
- **處理簡單 for/if/switch，符合 ECMAScript v2 語法限制**。

---

> 你可以將以上 Mermaid 流程圖直接貼入流程圖工具（如 mermaid.live、draw.io、ProcessOn 等）進行視覺化繪製。
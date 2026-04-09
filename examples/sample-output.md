# Skill Check 輸出範例

以下是對一個翻譯 Skill 跑 Analyze 模式的範例輸出：

---

## Skill Check: translator
Source: `.claude/skills/translator/SKILL.md` | Lines: 45 | Instructions: 8

### RPL Distribution
| Level | Count | % | Verdict |
|-------|-------|---|---------|
| RPL-1 | 3 | 37% | Too high |
| RPL-2 | 3 | 37% | Within limit |
| RPL-3 | 2 | 26% | Too low |

### Instruction Breakdown
| # | Instruction (summary) | Level | Rationale |
|---|---|---|---|
| 1 | 「你是專業翻譯師」 | RPL-1 | 角色標籤，移除後 AI 翻譯步驟不變 |
| 2 | 「精通中英日三語」 | RPL-1 | 能力宣稱，不改變翻譯方式 |
| 3 | 「保持原文語氣」 | RPL-2 | 方向指示，但沒說怎麼判斷語氣 |
| 4 | 「翻譯要自然流暢」 | RPL-2 | 態度指令，無具體標準 |
| 5 | 「遇到專業術語要查證」 | RPL-2 | 行為方向，但沒說怎麼查、查什麼 |
| 6 | 「先識別原文語言」 | RPL-3 (weak) | 有步驟但只有一步，缺依賴鏈 |
| 7 | 「使用 Glossary Consistency Check: 1. 提取所有專業術語 → 產出：術語清單 2. 對每個術語查找業界標準譯法 → 產出：標準 vs 自譯對照 3. 不一致的標記 flag」 | RPL-3 | 具名方法 + 3 步依賴鏈 + 產出術語對照表 |
| 8 | 「輸出格式：原文 / 翻譯 / 備註」 | RPL-3 | 改變輸出結構 |

### RPL-3 Validation
| RPL-3 Tool | Named? | Steps? | Distinguishable? | Scenario Trace | Status |
|---|---|---|---|---|---|
| 語言識別 (#6) | No | 1 (weak) | No | WITH/WITHOUT: 都會自動識別語言 | Weak |
| Glossary Consistency Check (#7) | Yes | 3 | Yes (術語對照表) | WITH: 產出標準 vs 自譯對照。WITHOUT: 直接翻，術語不一致 | Valid |
| 輸出格式 (#8) | No | N/A | Yes (三欄格式) | WITH: 原文/翻譯/備註。WITHOUT: 只有翻譯 | Valid |

### Diagnosis
- RPL-1 37% — 太高。#1 和 #2 是純裝飾，可直接刪除
- RPL-3 26% — 太低。核心翻譯邏輯缺乏 RPL-3 方法
- #6 是 weak RPL-3（只有一步，沒有依賴鏈）
- #3「保持原文語氣」可升級為 RPL-3：語氣判斷步驟（識別語域 → 對應目標語風格 → 回檢）

### Verdict
**WEAK** — RPL-1 > 10%, RPL-3 < 30%

---

*這個 Skill 的問題不是指令太少，是指令層次不對。刪掉 RPL-1、把 #3 和 #5 升級成 RPL-3，就能顯著改善翻譯品質的穩定性。*

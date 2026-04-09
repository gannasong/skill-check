<div align="center">

# skill-check

> *"You told AI to be an expert. It didn't become one — it just said 'expert' more often."*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)

<br>

你的 Skill 真的在改變 AI 的思考方式嗎？<br>
還是只是讓它**換了一套說詞**？<br>

**Skill Check** 用 RPL（Reasoning Path Levels）分析你的 Skill 每條指令的品質等級，<br>
告訴你哪些在起作用，哪些只是裝飾。

[安裝](#安裝) · [使用](#使用) · [RPL 是什麼](#rpl-是什麼) · [輸出範例](#輸出範例) · [English](#english)

</div>

---

## 安裝

```bash
# 全域安裝（所有專案可用）
git clone https://github.com/gannasong/skill-check ~/.claude/skills/skill-check

# 專案安裝（在 git repo 根目錄執行）
mkdir -p .claude/skills
git clone https://github.com/gannasong/skill-check .claude/skills/skill-check
```

---

## 使用

在 Claude Code 中輸入：

```
/skill-check path/to/your/SKILL.md
```

### 兩種模式

| 模式 | 說明 |
|------|------|
| **Analyze** | RPL 分佈報告 — 每條指令是哪個層級，整體評級 |
| **Optimize** | 分析 + 升級建議 — 刪 RPL-1、精簡 RPL-2、弱指令升級引導 |

### 評級標準

| 評級 | 條件 |
|------|------|
| **STRONG** | RPL-1 = 0%, RPL-3 ≥ 50%, 沒有弱 RPL-3 |
| **ADEQUATE** | RPL-1 ≤ 10%, RPL-3 ≥ 30% |
| **WEAK** | RPL-1 > 10% 或 RPL-3 < 30% |

---

## RPL 是什麼

同一個概念 — 第一性原理 — 三種寫法，三種完全不同的效果：

### RPL-1：角色扮演

```
你是 Elon Musk，使用第一性原理思考。
```

AI 會模仿 Musk 的語氣，回答裡多出「第一性原理」幾個字。但推理方式？**完全沒變。** 拿掉這句話，AI 的分析步驟一模一樣。

**白話：穿了戲服，台詞還是自己編。**

### RPL-2：思維框架

```
用第一性原理來分析這個問題。
```

比 RPL-1 好。AI 知道要拆解假設。但你沒說**怎麼拆**，所以每次走不同的路，結果時好時壞。

**白話：知道往哪走，但路自己選。**

### RPL-3：鏈式推理

```
1. 列出所有假設 → 產出：假設清單
2. 對每個假設找證據 → 產出：已驗證/未驗證分類
3. 只用已驗證假設推結論
```

Step 1 的輸出是 Step 2 的輸入。沒有假設清單，第二步做不了。AI 被迫走完整條路。

**白話：每句台詞寫好，照著走。穩定。**

### 重點

| | RPL-1 | RPL-2 | RPL-3 |
|---|---|---|---|
| 改變了什麼 | 用詞 | 方向 | 推理路徑 |
| 穩定度 | 無效 | 時好時壞 | 穩定 |
| 理想佔比 | 0% | 適量 | ≥ 50% |

**大部分人的 Skill 充滿 RPL-1 和 RPL-2，所以 AI 表現不穩定。** 不是 AI 的問題 — 是指令的層次不對。

---

## 輸出範例

詳見 [examples/sample-output.md](examples/sample-output.md)

簡要預覽：

```
## Skill Check: translator
Source: .claude/skills/translator/SKILL.md | Lines: 45 | Instructions: 8

### RPL Distribution
| Level | Count | %  | Verdict   |
|-------|-------|----|-----------|
| RPL-1 | 3     | 37% | Too high |
| RPL-2 | 3     | 37% | OK       |
| RPL-3 | 2     | 26% | Too low  |

### Verdict
WEAK — RPL-1 > 10%, RPL-3 < 30%
```

---

## 常見問題

**Q: RPL-2 不好嗎？**<br>
不是不好。RPL-2 設定思考方向，對 coach 類 Skill 有用。但太多會互相稀釋 — 7 條態度指令 × 70% 遵從 = 沒有一條被強烈執行。精簡到 3-5 條最有效。

**Q: 怎麼把 RPL-1 升級成 RPL-3？**<br>
問自己：「這個角色會做什麼**別人不會做的事**？」然後把那件事寫成步驟。<br>
例：「你是 code reviewer」→「1. 列出所有 inputs 2. 對每個測邊界值 3. 追蹤未驗證路徑」

**Q: 跑出來 WEAK 怎麼辦？**<br>
正常。大部分 Skill 第一次跑都是 WEAK。用 Optimize 模式看建議，一次改一兩條最關鍵的就好。

---

## 專案結構

```
skill-check/
├── SKILL.md                # Skill 主檔（純英文，自包含）
├── README.md               # 說明文件
├── LICENSE                 # MIT
└── examples/
    └── sample-output.md    # 輸出範例
```

---

<a name="english"></a>

## English

### What is this?

**Skill Check** analyzes your Claude Code skills using RPL (Reasoning Path Levels) — a framework that classifies each instruction by how deeply it actually changes AI reasoning.

- **RPL-1 (Role-Playing)**: "You are an expert" → AI uses the word "expert" more. Reasoning unchanged.
- **RPL-2 (Thinking Framework)**: "Use first principles" → AI knows the direction but picks a different path each time.
- **RPL-3 (Chain Reasoning)**: "1. List assumptions → 2. Find evidence for each → 3. Conclude from verified only" → AI is forced to follow the chain. Stable output.

Most skills are full of RPL-1 and RPL-2. That's why AI performance is inconsistent. Upgrading key instructions to RPL-3 makes it stable.

### Install

```bash
git clone https://github.com/gannasong/skill-check ~/.claude/skills/skill-check
```

### Use

```
/skill-check path/to/your/SKILL.md
```

Two modes: **Analyze** (distribution report + verdict) and **Optimize** (analysis + upgrade guidance).

### Verdict Scale

| Verdict | Criteria |
|---------|----------|
| STRONG | RPL-1 = 0%, RPL-3 >= 50%, no weak RPL-3 |
| ADEQUATE | RPL-1 <= 10%, RPL-3 >= 30% |
| WEAK | RPL-1 > 10% or RPL-3 < 30% |

---

MIT License © [Miguel Lin](https://github.com/gannasong)

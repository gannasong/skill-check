<div align="center">

# skill-check

> *"You told AI to be an expert. It didn't become one — it just said 'expert' more often."*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)

<br>

Is your skill actually changing how AI thinks?<br>
Or is it just **changing the words it uses**?<br>

**Skill Check** analyzes your Claude Code skills using RPL (Reasoning Path Levels) — a framework that classifies each instruction by how deeply it actually changes AI reasoning.

[Install](#install) · [Usage](#usage) · [What is RPL](#what-is-rpl) · [中文](README.md)

</div>

---

## Install

```bash
# Global install (available in all projects)
git clone https://github.com/gannasong/skill-check ~/.claude/skills/skill-check

# Project install (run in git repo root)
mkdir -p .claude/skills
git clone https://github.com/gannasong/skill-check .claude/skills/skill-check
```

---

## Usage

In Claude Code:

```
/skill-check path/to/your/SKILL.md
```

### Two Modes

| Mode | Description |
|------|-------------|
| **Analyze** | RPL distribution report — each instruction's level + overall verdict |
| **Optimize** | Analysis + upgrade guidance — delete RPL-1, trim RPL-2, upgrade weak instructions |

### Verdict Scale

| Verdict | Criteria |
|---------|----------|
| **STRONG** | RPL-1 = 0%, RPL-3 >= 50%, no weak RPL-3 |
| **ADEQUATE** | RPL-1 <= 10%, RPL-3 >= 30% |
| **WEAK** | RPL-1 > 10% or RPL-3 < 30% |

---

## What is RPL

Same concept — First Principles Thinking — three ways to write it, three completely different results:

### RPL-1: Role-Playing

```
You are Elon Musk. Think in first principles.
```

AI mimics Musk's tone. Uses "first principles" more often. But its reasoning? **Unchanged.** Remove this line, AI's analysis steps are identical.

**In plain words: wearing a costume, improvising the lines.**

### RPL-2: Thinking Framework

```
Analyze this problem using first principles.
```

Better than RPL-1. AI knows to question assumptions. But you didn't say **how**, so it takes a different path each time. Results are inconsistent.

**In plain words: knows where to go, but picks its own route.**

### RPL-3: Chain Reasoning

```
1. List all assumptions → output: assumption list
2. For each assumption, find evidence → output: verified/unverified classification
3. Derive conclusions using only verified assumptions
```

Step 1's output feeds Step 2. Without the assumption list, step 2 can't run. AI is forced to follow the entire chain.

**In plain words: every line is scripted. Follow it. Stable.**

### The Point

| | RPL-1 | RPL-2 | RPL-3 |
|---|---|---|---|
| Changes | Wording | Direction | Reasoning path |
| Stability | Ineffective | Inconsistent | Stable |
| Ideal ratio | 0% | Moderate | >= 50% |

**Most skills are full of RPL-1 and RPL-2. That's why AI performance is inconsistent.** It's not the AI's fault — it's the instruction level.

---

## Example Output

See [examples/sample-output.md](examples/sample-output.md)

Quick preview:

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

## FAQ

**Q: Is RPL-2 bad?**<br>
No. RPL-2 sets thinking direction — useful for coach-type skills. But too many dilute each other: 7 attitude instructions × 70% compliance = none strongly enforced. Keep 3-5 max.

**Q: How do I upgrade RPL-1 to RPL-3?**<br>
Ask yourself: "What would this role DO that others wouldn't?" Then write those actions as steps.<br>
Example: "You are a code reviewer" → "1. List all inputs 2. Test each with boundary values 3. Trace unvalidated paths"

**Q: My skill scored WEAK. Now what?**<br>
Normal. Most skills score WEAK on first check. Use Optimize mode, fix 1-2 key instructions at a time.

---

## Project Structure

```
skill-check/
├── SKILL.md                # Main skill file (English, self-contained)
├── README.md               # Documentation (繁體中文)
├── README_EN.md            # Documentation (English)
├── LICENSE                 # MIT
└── examples/
    └── sample-output.md    # Sample output
```

---

MIT License © [Miguel Lin](https://github.com/gannasong)

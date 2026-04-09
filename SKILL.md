---
name: skill-check
description: "Analyze any Claude Code skill's quality using RPL (Reasoning Path Levels). Classifies each instruction as RPL-1/RPL-2/RPL-3, validates RPL-3 effectiveness, and provides upgrade guidance. Use when you want to know if your skill is actually working or just looking good."
version: "1.0.0"
user-invocable: true
allowed-tools: Read, Glob, Grep
---

# Skill Check — RPL Quality Diagnosis

Analyze any skill's instruction quality using Reasoning Path Levels (RPL).

**What is RPL?** The same instruction can be written at three levels. Each level changes how deeply the AI actually thinks — not just what words it uses.

Example with "First Principles Thinking":

| Level | How You Write It | What AI Actually Does |
|-------|-----------------|----------------------|
| RPL-1 | "You are Elon Musk. Think in first principles." | Mimics Musk's tone. Uses "first principles" more often. Reasoning unchanged. |
| RPL-2 | "Analyze this problem using first principles." | Knows to question assumptions. But HOW it does that varies each time. |
| RPL-3 | "1. List all assumptions → output: assumption list → 2. For each assumption, find supporting/opposing evidence → output: verified/unverified classification → 3. Derive conclusions using only verified assumptions" | Forced to follow the chain. Step 1 output feeds Step 2. Can't skip. Stable. |

**Key insight**: RPL-1 changes words. RPL-2 changes direction. RPL-3 changes the reasoning path.

## Input

`/skill-check [file-path]` — path to any SKILL.md file. If none provided, ask user.

## Step 1: Mode Selection

```
Which check?
(1) Analyze — RPL distribution report + verdict
(2) Optimize — analysis + upgrade suggestions
```

## Step 2: RPL Analysis (both modes)

### 2a. Parse Instructions

Break the skill into individual instructions/directives. Each bullet, rule, step, or distinct directive = one instruction.

### 2b. Classify Each Instruction

```
Q1: Remove this instruction — do the model's analysis STEPS change?
|- Only wording changes -> RPL-1 (Role-Playing)
+- Behavior changes ->
    Q2: Does it tell HOW to do something, or WHAT attitude to have?
    |- Attitude/willingness -> RPL-2 (Direction)
    +- Concrete steps -> RPL-3 (Reasoning Chain)
```

Edge case: instruction with both stance AND concrete steps -> RPL-3.

### 2c. Validate RPL-3s

Two stages. Stage 1 checks structure. Stage 2 checks behavior.

**Stage 1 — 3-Element Check:**

| Element | Question | Missing = |
|---------|----------|-----------|
| Named method | Does it have a name? | Weak RPL-3 |
| Concrete steps | Are there ordered actions (max 5)? | Weak RPL-3 |
| Distinguishable output | Would output look structurally different? | Weak RPL-3 |

All 3 present -> proceed to Stage 2. Missing any -> "weak RPL-3".

**Stage 2 — Behavioral Trace:**
1. Pick a concrete input scenario relevant to the skill's domain
2. Trace WITH this RPL-3 active — what structural element appears in the output?
3. Trace WITHOUT — does that structural element disappear?
4. YES (structure changes) -> valid RPL-3
5. NO (same structure, different words) -> downgrade to "cosmetic RPL-3" (functionally RPL-2)

### 2d. Output

```markdown
## Skill Check: {name}
Source: {path} | Lines: {n} | Instructions: {n}

### RPL Distribution
| Level | Count | % | Verdict |

### Instruction Breakdown
| # | Instruction (summary) | Level | Rationale |

### RPL-3 Validation
| RPL-3 Tool | Named? | Steps? | Distinguishable? | Scenario Trace | Status |

### Diagnosis
{Issues found — see criteria below}

RPL-2 limits by skill type:
- Interactive Agent / Coach (multi-turn, cross-session): RPL-2 max 5
- Task Worker / One-shot skill: RPL-2 max 3

### Verdict
STRONG / ADEQUATE / WEAK

- STRONG: RPL-1 = 0%, RPL-2 within limit, RPL-3 >= 50%, no weak RPL-3
- ADEQUATE: RPL-1 <= 10%, RPL-3 >= 30%
- WEAK: RPL-1 > 10% or RPL-3 < 30%
```

**Analyze mode ends here. Optimize mode continues to Step 3.**

## Step 3: Optimize (Optimize mode only)

Three operations in order:

### 3a. DELETE RPL-1

For each RPL-1 instruction:
- Is there already an RPL-3 that covers this? -> delete the RPL-1 (wasted tokens)
- No coverage? -> upgrade it (see 3c) or delete it

### 3b. TRIM RPL-2

Count RPL-2 instructions against the limit (Task Worker max 3, Interactive Agent max 5).

Over limit? For each RPL-2, ask: "If I remove this, does the skill's behavior observably change?"
- No -> delete
- Yes -> keep

Keep only the ones with strongest observable impact.

### 3c. Upgrade Guidance

For each RPL-1 or weak RPL-2 that needs upgrading to RPL-3:

1. **Identify the failure mode**: What does the AI get wrong when following this instruction?
2. **Design steps that prevent that failure**: What analysis steps would force the AI to avoid that specific mistake?
3. **Check three elements**:
   - Does it have a name? (Named method)
   - Are there ordered steps (max 5) where each step's output feeds the next? (Dependency chain)
   - Would the output look structurally different from a skill without this tool? (Distinguishable)

**Common upgrade patterns:**

| Your instruction looks like... | Upgrade direction |
|-------------------------------|-------------------|
| Role label ("you are a reviewer") | What would this role DO that others wouldn't? Write those steps. |
| Topic keyword ("focus on security") | What specifically would the AI check? In what order? Write that. |
| Framework name without steps ("use SWOT analysis") | Write SWOT as a dependency chain: S output feeds W input, etc. |
| Attitude directive ("be thorough") | What does "thorough" mean concretely? How many layers? What triggers stopping? |
| Tool list used all at once | Add trigger conditions: when to use which tool, not all together. |

### 3d. Verify

After changes:
- RPL-1% at most 5%?
- RPL-3% at least 50%?
- RPL-2 within type limit?

Present upgraded version for user review.

## Notes

- RPL-1 ideal = 0%. Any RPL-1 = wasted tokens that don't change AI behavior.
- RPL-2 is not "bad" — it sets thinking direction. But too many dilute each other (Prompt Dilution). Keep only the ones that visibly change behavior.
- RPL-3 is about **dependency**, not just steps. "1. Do A 2. Do B 3. Do C" where each step is independent = not RPL-3. "1. Do A -> output X -> 2. Use X to do B -> output Y -> 3. Use Y to do C" = RPL-3.
- This tool checks instruction quality. It does not guarantee runtime effectiveness — a well-structured RPL-3 can still use a bad method. But a badly-structured instruction (RPL-1) will never produce deep analysis.

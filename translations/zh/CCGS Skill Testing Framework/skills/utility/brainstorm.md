# Skill 测试 Spec: /brainstorm

## Skill 摘要

`/brainstorm` facilitates guided game concept ideation. It presents 2-4 concept
options with pros/cons, lets the user choose and refine a concept, and produces
a structured `design/gdd/game-concept.md` document. The skill is collaborative —
it asks questions before proposing options and iterates until the user approves
a concept direction.

In `full` review mode, four director gates spawn in parallel after the concept
is drafted: CD-PILLARS (creative-director), AD-CONCEPT-VISUAL (art-director),
TD-FEASIBILITY (technical-director), and PR-SCOPE (producer). In `lean` mode,
all 4 inline gates are skipped (lean mode only runs PHASE-GATEs, and brainstorm
has none). In `solo` mode, all gates are skipped. The skill asks "May I write"
before writing `design/gdd/game-concept.md`.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: APPROVED, REJECTED, CONCERNS
- [ ] Contains "May I write" collaborative protocol language (for game-concept.md)
- [ ] Has a next-step handoff at the end (`/map-systems`)
- [ ] Documents 4 director gates in full mode: CD-PILLARS, AD-CONCEPT-VISUAL, TD-FEASIBILITY, PR-SCOPE
- [ ] Documents that all 4 gates are skipped in lean and solo modes

---

## Director Gate 检查

In `full` mode: CD-PILLARS, AD-CONCEPT-VISUAL, TD-FEASIBILITY, and PR-SCOPE
spawn in parallel after the concept draft is approved by the user.

In `lean` mode: all 4 inline gates are skipped (brainstorm has no PHASE-GATEs,
so lean mode skips everything). Output notes all 4 as: "[GATE-ID] skipped — lean mode".

In `solo` mode: all 4 gates are skipped. Output notes all 4 as: "[GATE-ID] skipped — solo mode".

---

## 测试用例

### 用例 1: Happy Path — Full mode, 3 concepts, user picks one, all 4 directors approve

**设置：**
- No existing `design/gdd/game-concept.md`
- `production/session-state/review-mode.txt` contains `full`

**输入：** `/brainstorm`

**预期行为：**
1. Skill asks the user questions about genre, scope, and target feeling
2. Skill presents 3 concept options with pros/cons each
3. User selects one concept
4. Skill elaborates the chosen concept into a structured draft
5. All 4 director gates spawn in parallel: CD-PILLARS, AD-CONCEPT-VISUAL, TD-FEASIBILITY, PR-SCOPE
6. All 4 return APPROVED
7. Skill asks "May I write `design/gdd/game-concept.md`?"
8. Concept written after approval

**断言：**
- [ ] Exactly 3 concept options are presented (not 1, not 5+)
- [ ] All 4 director gates spawn in parallel (not sequentially)
- [ ] All 4 gates complete before the "May I write" ask
- [ ] "May I write `design/gdd/game-concept.md`?" is asked before writing
- [ ] Concept file is NOT written without user approval
- [ ] Next-step handoff to `/map-systems` is present

---

### 用例 2: Failure Path — CD-PILLARS returns REJECT

**设置：**
- Concept draft is complete
- `production/session-state/review-mode.txt` contains `full`
- CD-PILLARS gate returns REJECT: "The concept has no identifiable creative pillar"

**输入：** `/brainstorm`

**预期行为：**
1. CD-PILLARS gate returns REJECT with specific feedback
2. Skill surfaces the rejection to the user
3. Concept is NOT written to file
4. User is asked: rethink the concept direction, or override the rejection
5. If rethinking: skill returns to the concept options phase

**断言：**
- [ ] Concept is NOT written when CD-PILLARS returns REJECT
- [ ] Rejection feedback is shown to the user verbatim
- [ ] User is given the option to rethink or override
- [ ] Skill returns to concept ideation phase if user chooses to rethink

---

### 用例 3: Lean Mode — All 4 gates skipped; concept written after user confirms

**设置：**
- No existing game concept
- `production/session-state/review-mode.txt` contains `lean`

**输入：** `/brainstorm`

**预期行为：**
1. Concept options are presented and user selects one
2. Concept is elaborated into a structured draft
3. All 4 director gates are skipped — each noted: "[GATE-ID] skipped — lean mode"
4. Skill asks user to confirm the concept is ready to write
5. "May I write `design/gdd/game-concept.md`?" asked after confirmation
6. Concept written after approval

**断言：**
- [ ] All 4 gate skip notes appear: "CD-PILLARS skipped — lean mode", "AD-CONCEPT-VISUAL skipped — lean mode", "TD-FEASIBILITY skipped — lean mode", "PR-SCOPE skipped — lean mode"
- [ ] Concept is written after user confirmation only (no director approval needed in lean)
- [ ] "May I write" is still asked before writing

---

### 用例 4: Solo Mode — All gates skipped; concept written with only user approval

**设置：**
- No existing game concept
- `production/session-state/review-mode.txt` contains `solo`

**输入：** `/brainstorm`

**预期行为：**
1. Concept options are presented and user selects one
2. Concept draft is shown to user
3. All 4 director gates are skipped — each noted with "solo mode"
4. "May I write `design/gdd/game-concept.md`?" asked
5. Concept written after user approval

**断言：**
- [ ] All 4 skip notes appear with "solo mode" label
- [ ] No director agents are spawned
- [ ] Concept is written with only user approval
- [ ] Behavior is otherwise equivalent to lean mode for this skill

---

### 用例 5: Director Gate — PR-SCOPE returns CONCERNS (scope too large)

**设置：**
- Concept draft is complete
- `production/session-state/review-mode.txt` contains `full`
- PR-SCOPE gate returns CONCERNS: "The concept scope would require 18+ months for a solo developer"

**输入：** `/brainstorm`

**预期行为：**
1. PR-SCOPE gate returns CONCERNS with specific scope feedback
2. Skill surfaces the scope concerns to the user
3. Scope concerns are documented in the concept draft before writing
4. User is asked: reduce scope, accept concerns and document them, or rethink
5. If concerns are accepted: concept is written with a "Scope Risk" note embedded

**断言：**
- [ ] PR-SCOPE concerns are shown to the user before the "May I write" ask
- [ ] Skill does NOT write concept without surfacing scope concerns
- [ ] If user accepts: scope concerns are documented in the concept file
- [ ] Skill does NOT auto-reject a concept due to PR-SCOPE CONCERNS (user decides)

---

## 协议合规性

- [ ] Presents 2-4 concept options with pros/cons before user commits
- [ ] User confirms concept direction before director gates are invoked
- [ ] All 4 director gates spawn in parallel in full mode
- [ ] All 4 gates skipped in lean AND solo mode — each noted by name
- [ ] "May I write `design/gdd/game-concept.md`?" asked before writing
- [ ] Ends with next-step handoff: `/map-systems`

---

## 覆盖说明

- AD-CONCEPT-VISUAL gate (art director feasibility) is grouped with the other
  3 gates in the parallel spawn — not independently fixture-tested.
- The iterative concept refinement loop (user rejects all options, skill
  generates new ones) is not fixture-tested — it follows the same pattern as
  the option selection phase.
- The game-concept.md document structure (required sections) is defined in the
  skill body and not re-enumerated in test assertions.

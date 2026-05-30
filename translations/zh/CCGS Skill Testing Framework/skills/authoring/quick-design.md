# Skill 测试 Spec: /quick-design

## Skill 摘要

`/quick-design` produces a lightweight design spec for features too small to
warrant a full 8-section GDD. The target scope is under 4 hours of design time
for a single-system feature. Instead of the full 8-section GDD format, the
quick-design spec uses a streamlined 3-section format: Overview, Rules, and
Acceptance Criteria.

The skill has no director gates — adding gate overhead would defeat the purpose
of a lightweight design tool. The skill asks "May I write" before writing the
design note to `design/quick-notes/[name].md`. If the feature scope is too large
for a quick-design, the skill redirects to `/design-system` instead.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: CREATED, BLOCKED, REDIRECTED
- [ ] Contains "May I write" collaborative protocol language (for quick-note file)
- [ ] Has a next-step handoff at the end
- [ ] Explicitly notes: no director gates (lightweight skill by design)
- [ ] Mentions scope check: redirects to `/design-system` if scope exceeds sub-4h threshold

---

## Director Gate 检查

No director gates — this skill spawns no director gate agents. The lightweight
nature of quick-design means director gate overhead is intentionally absent.
Full GDD review is not needed for sub-4-hour single-system features.

---

## 测试用例

### 用例 1: Happy Path — Small UI change produces a 3-section spec

**设置：**
- No existing quick-note for the target feature
- Feature is clearly scoped: a single UI element change with no cross-system impact

**输入：** `/quick-design [feature-name]`

**预期行为：**
1. Skill asks scoping questions: what system, what change, what is the acceptance signal
2. Skill determines scope is within the sub-4h threshold
3. Skill drafts a 3-section spec: Overview, Rules, Acceptance Criteria
4. Draft is shown to user
5. "May I write `design/quick-notes/[name].md`?" is asked
6. File is written after approval

**断言：**
- [ ] Spec contains exactly 3 sections: Overview, Rules, Acceptance Criteria
- [ ] Draft is shown to user before "May I write" ask
- [ ] "May I write `design/quick-notes/[name].md`?" is asked before writing
- [ ] File is written to the correct path: `design/quick-notes/[name].md`
- [ ] Verdict is CREATED after successful write

---

### 用例 2: Failure Path — Scope check fails; redirected to /design-system

**设置：**
- Feature described spans multiple systems or would take more than 4 hours of design time
  (e.g., "redesign the entire combat system" or "new progression mechanic affecting all classes")

**输入：** `/quick-design [large-feature]`

**预期行为：**
1. Skill asks scoping questions
2. Skill determines scope exceeds the sub-4h / single-system threshold
3. Skill outputs: "This feature is too large for a quick-design. Use `/design-system [name]` for a full GDD."
4. Skill does NOT write a quick-note file
5. Verdict is REDIRECTED

**断言：**
- [ ] Skill detects the scope excess and stops before drafting
- [ ] Message explicitly names `/design-system` as the correct alternative
- [ ] No quick-note file is written
- [ ] Verdict is REDIRECTED (not CREATED or BLOCKED)

---

### 用例 3: Edge Case — File already exists; offered to update

**设置：**
- `design/quick-notes/[name].md` already exists from a previous session

**输入：** `/quick-design [name]`

**预期行为：**
1. Skill detects existing quick-note file and reads its current content
2. Skill asks: "[name].md already exists. Update it, or create a new version?"
3. User selects update
4. Skill shows the existing spec and asks which section to revise
5. Updated spec is shown, "May I write?" asked, file updated after approval

**断言：**
- [ ] Skill detects and reads the existing file before offering to update
- [ ] User is offered update or create-new options — not auto-overwritten
- [ ] Only the revised section is updated (or the whole spec if user chooses full rewrite)
- [ ] "May I write" is asked before overwriting the existing file

---

### 用例 4: Edge Case — No argument provided

**设置：**
- `design/quick-notes/` directory may or may not exist

**输入：** `/quick-design` (no argument)

**预期行为：**
1. Skill detects no argument is provided
2. Skill outputs a usage error: "No feature name specified. Usage: /quick-design [feature-name]"
3. Skill provides an example: `/quick-design pause-menu-settings`
4. No file is created

**断言：**
- [ ] Skill outputs a usage error when no argument is given
- [ ] A usage example is shown with the correct format
- [ ] No quick-note file is written
- [ ] Skill does NOT silently pick a feature name or default to any action

---

### 用例 5: Director Gate — No gate spawned; explicitly noted for sub-4h features

**设置：**
- Feature is within scope for quick-design
- `production/session-state/review-mode.txt` exists with `full`

**输入：** `/quick-design [feature-name]`

**预期行为：**
1. Skill asks scoping questions and determines scope is within threshold
2. Skill does NOT read `production/session-state/review-mode.txt`
3. Skill does NOT spawn any director gate agent
4. Spec is drafted, "May I write" asked, file written after approval
5. Output explicitly notes: "No director gate review — quick-design is for sub-4h features"

**断言：**
- [ ] No director gate agents are spawned (no CD-, TD-, PR-, AD- prefixed gates)
- [ ] Skill does NOT read `production/session-state/review-mode.txt`
- [ ] Output contains a note explaining why no gate review is needed
- [ ] Review mode has no effect on this skill's behavior
- [ ] Full GDD review path (`/design-system`) is mentioned as the alternative for larger features

---

## 协议合规性

- [ ] Scope check runs before drafting (redirects to `/design-system` if scope too large)
- [ ] 3-section format used (Overview, Rules, Acceptance Criteria) — NOT the 8-section GDD format
- [ ] Draft shown to user before "May I write" ask
- [ ] "May I write `design/quick-notes/[name].md`?" asked before writing
- [ ] No director gates — no review-mode.txt read
- [ ] Ends with next-step handoff (e.g., proceed to implementation or `/dev-story`)

---

## 覆盖说明

- The scope threshold heuristic (sub-4h, single-system) is a judgment call —
  the skill's internal check is the authoritative definition and is not
  independently tested by counting hours.
- The `design/quick-notes/` directory is created automatically if it does not
  exist — this filesystem behavior is not independently tested here.
- Integration with the story pipeline (can a quick-design generate a story
  directly?) is out of scope for this spec — quick-designs are standalone.

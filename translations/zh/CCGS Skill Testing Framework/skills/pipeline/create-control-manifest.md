# Skill 测试 Spec: /create-control-manifest

## Skill 摘要

`/create-control-manifest` reads all Accepted ADRs from `docs/architecture/` and
generates a control manifest — a summary document that captures all architectural
constraints, required patterns, and forbidden patterns in one place. The manifest
is the reference document that story authors use when writing story files, ensuring
stories inherit the correct architectural rules without having to read all ADRs
individually.

The skill only includes Accepted ADRs; Proposed ADRs are excluded and noted. It
has no director gates. The skill asks "May I write" before writing
`docs/architecture/control-manifest.md`.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: CREATED, BLOCKED
- [ ] Contains "May I write" collaborative protocol language (for control-manifest.md)
- [ ] Has a next-step handoff at the end (`/create-epics` or `/create-stories`)
- [ ] Documents that only Accepted ADRs are included (not Proposed)

---

## Director Gate 检查

No director gates — this skill spawns no director gate agents. The control
manifest is a mechanical extraction from Accepted ADRs; no creative or technical
review gate is needed.

---

## 测试用例

### 用例 1: Happy Path — 4 Accepted ADRs create a correct manifest

**设置：**
- `docs/architecture/` contains 4 ADR files, all with `Status: Accepted`
- Each ADR has a "Required Patterns" and/or "Forbidden Patterns" section
- No existing `docs/architecture/control-manifest.md`

**输入：** `/create-control-manifest`

**预期行为：**
1. Skill reads all ADR files in `docs/architecture/`
2. Extracts Required Patterns, Forbidden Patterns, and key constraints from each
3. Drafts the manifest with correct section structure
4. Shows the draft manifest to the user
5. Asks "May I write `docs/architecture/control-manifest.md`?"
6. Writes the manifest after approval

**断言：**
- [ ] All 4 Accepted ADRs are represented in the manifest
- [ ] Manifest includes distinct sections for Required Patterns and Forbidden Patterns
- [ ] Manifest includes the source ADR number for each constraint
- [ ] "May I write" is asked before writing
- [ ] Skill does NOT write without approval
- [ ] Verdict is CREATED after writing

---

### 用例 2: Failure Path — No ADRs found

**设置：**
- `docs/architecture/` directory exists but contains no ADR files

**输入：** `/create-control-manifest`

**预期行为：**
1. Skill reads `docs/architecture/` and finds no ADR files
2. Skill outputs: "No ADRs found. Run `/architecture-decision` to create ADRs before generating the control manifest."
3. Skill exits without creating any file
4. Verdict is BLOCKED

**断言：**
- [ ] Skill outputs a clear error when no ADRs are found
- [ ] No control manifest file is written
- [ ] Skill recommends `/architecture-decision` as the next action
- [ ] Verdict is BLOCKED (not an error crash)

---

### 用例 3: Mixed ADR Statuses — Only Accepted ADRs included

**设置：**
- `docs/architecture/` contains 3 Accepted ADRs and 2 Proposed ADRs

**输入：** `/create-control-manifest`

**预期行为：**
1. Skill reads all ADR files and filters by Status: Accepted
2. Manifest is drafted from the 3 Accepted ADRs only
3. Output notes: "2 Proposed ADRs were excluded: [adr-NNN-name, adr-NNN-name]"
4. User sees which ADRs were excluded before approving the write
5. Asks "May I write `docs/architecture/control-manifest.md`?"

**断言：**
- [ ] Only the 3 Accepted ADRs appear in the manifest content
- [ ] Excluded Proposed ADRs are listed by name in the output
- [ ] User sees the exclusion list before approving the write
- [ ] Skill does NOT silently omit Proposed ADRs without noting them

---

### 用例 4: Edge Case — Manifest already exists

**设置：**
- `docs/architecture/control-manifest.md` already exists (version 1, dated last week)
- `docs/architecture/` contains Accepted ADRs (some new since last manifest)

**输入：** `/create-control-manifest`

**预期行为：**
1. Skill detects existing manifest and reads its version number / date
2. Skill offers to regenerate: "control-manifest.md already exists (v1, [date]). Regenerate with current ADRs?"
3. If user confirms: skill drafts updated manifest, increments version number
4. Asks "May I write `docs/architecture/control-manifest.md`?" (overwrite)
5. Writes updated manifest after approval

**断言：**
- [ ] Skill reads and reports the existing manifest version before offering to regenerate
- [ ] User is offered a regenerate/skip choice — not auto-overwritten
- [ ] Updated manifest has an incremented version number
- [ ] "May I write" is asked before overwriting the existing file

---

### 用例 5: Director Gate — No gate spawned; no review-mode.txt read

**设置：**
- 4 Accepted ADRs exist
- `production/session-state/review-mode.txt` exists with `full`

**输入：** `/create-control-manifest`

**预期行为：**
1. Skill reads ADRs and drafts manifest
2. Skill does NOT read `production/session-state/review-mode.txt`
3. No director gate agents are spawned at any point
4. Skill proceeds directly to "May I write" after drafting
5. Review mode setting has no effect on this skill's behavior

**断言：**
- [ ] No director gate agents are spawned (no CD-, TD-, PR-, AD- prefixed gates)
- [ ] Skill does NOT read `production/session-state/review-mode.txt`
- [ ] Output contains no "Gate: [GATE-ID]" or gate-skipped entries
- [ ] The manifest is generated from ADRs alone, with no external gate review

---

## 协议合规性

- [ ] Reads all ADR files before drafting manifest
- [ ] Only Accepted ADRs included — Proposed ones noted as excluded
- [ ] Manifest draft shown to user before "May I write" ask
- [ ] "May I write `docs/architecture/control-manifest.md`?" asked before writing
- [ ] No director gates — no review-mode.txt read
- [ ] Ends with next-step handoff: `/create-epics` or `/create-stories`

---

## 覆盖说明

- The exact section structure of the generated manifest (constraint tables, pattern
  lists) is defined by the skill body and not re-enumerated in test assertions.
- The `version` field incrementing logic (v1 → v2) is tested via Case 4 but exact
  version numbering format is not fixture-locked.
- ADR parsing (extracting Required/Forbidden Patterns) depends on consistent ADR
  structure — tested implicitly via Case 1's fixture.

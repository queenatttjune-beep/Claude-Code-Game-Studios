# Skill 测试 Spec: /prototype

## Skill 摘要

`/prototype` manages a rapid prototyping workflow for validating a game mechanic
before committing to full production implementation. Prototypes are created in
`prototypes/[mechanic-name]/` and are intentionally disposable — coding standards
are relaxed (no ADR required, AC can be minimal, hardcoded values acceptable).
After implementation, the skill produces a findings document summarizing what
was learned and recommending next steps.

The skill asks "May I write to `prototypes/[name]/`?" before creating files. If a
prototype already exists, the skill offers to extend, replace, or archive. No
director gates apply. 裁决： PROTOTYPE COMPLETE (prototype built and findings
documented) or PROTOTYPE ABANDONED (mechanic found to be unworkable).

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: PROTOTYPE COMPLETE, PROTOTYPE ABANDONED
- [ ] Contains "May I write" language before creating prototype files
- [ ] Has a next-step handoff (e.g., `/design-system` to formalize, or archive)

---

## Director Gate 检查

无。 Prototypes are throwaway validation artifacts. No director gates apply.

---

## 测试用例

### 用例 1: Happy Path — Mechanic concept prototyped, findings documented

**设置：**
- `prototypes/` directory exists
- No existing prototype for "grapple-hook"

**输入：** `/prototype grapple-hook`

**预期行为：**
1. Skill asks "May I write to `prototypes/grapple-hook/`?"
2. After approval: creates `prototypes/grapple-hook/` directory and basic
   implementation skeleton (main scene, player controller extension)
3. Skill implements a minimal grapple hook mechanic (intentionally rough — no
   polish, hardcoded values acceptable)
4. Skill produces `prototypes/grapple-hook/findings.md` with:
   - What was tested
   - What worked
   - What didn't work
   - Recommendation (proceed / abandon / revise concept)
5. Verdict is PROTOTYPE COMPLETE

**断言：**
- [ ] "May I write to `prototypes/grapple-hook/`?" is asked before any files are created
- [ ] Implementation is isolated to `prototypes/` (not `src/`)
- [ ] `findings.md` is created with at minimum: tested/worked/didn't-work/recommendation
- [ ] Verdict is PROTOTYPE COMPLETE

---

### 用例 2: Prototype Already Exists — Offers Extend, Replace, or Archive

**设置：**
- `prototypes/grapple-hook/` already exists from a previous prototype session
- It contains a basic implementation and a findings.md

**输入：** `/prototype grapple-hook`

**预期行为：**
1. Skill detects existing `prototypes/grapple-hook/` directory
2. Skill reports: "Prototype already exists for grapple-hook"
3. Skill presents 3 options:
   - Extend: add new features to the existing prototype
   - Replace: start fresh (asks "May I replace `prototypes/grapple-hook/`?")
   - Archive: move to `prototypes/archive/grapple-hook/` and start fresh
4. User selects; skill proceeds accordingly

**断言：**
- [ ] Existing prototype is detected and reported
- [ ] Exactly 3 options are presented (extend, replace, archive)
- [ ] Replace path includes a "May I replace" confirmation
- [ ] Archive path moves (not deletes) the existing prototype

---

### 用例 3: Prototype Validates Mechanic — Recommends Proceeding to Production

**设置：**
- Prototype implementation complete
- Findings: grapple hook mechanic is fun and technically feasible

**输入：** `/prototype grapple-hook` (prototype session complete)

**预期行为：**
1. After prototype is built and tested, findings are summarized
2. Recommendation in findings.md: "Mechanic validated — recommend proceeding
   to `/design-system` for full specification"
3. Skill handoff message explicitly suggests `/design-system grapple-hook`
4. Verdict is PROTOTYPE COMPLETE

**断言：**
- [ ] `findings.md` contains an explicit recommendation
- [ ] Recommendation references `/design-system` when mechanic is validated
- [ ] Handoff message echoes the recommendation
- [ ] Verdict is PROTOTYPE COMPLETE (not PROTOTYPE ABANDONED)

---

### 用例 4: Prototype Reveals Mechanic is Unworkable — PROTOTYPE ABANDONED

**设置：**
- Prototype implemented for "procedural-dialogue"
- After testing: the mechanic creates incoherent dialogue trees and is
  frustrating to play

**输入：** `/prototype procedural-dialogue`

**预期行为：**
1. Prototype is built
2. Findings document the failure: incoherent output, player confusion, technical complexity
3. Recommendation in findings.md: "Mechanic not viable — abandoning"
4. `findings.md` documents the specific reasons the mechanic failed
5. Skill suggests alternatives in the handoff (e.g., curated dialogue instead)
6. Verdict is PROTOTYPE ABANDONED

**断言：**
- [ ] Verdict is PROTOTYPE ABANDONED (not PROTOTYPE COMPLETE)
- [ ] `findings.md` documents specific failure reasons (not vague)
- [ ] Alternative approaches are suggested in the handoff
- [ ] Prototype files are retained (not deleted) for reference

---

### 用例 5: Director Gate Check — No gate; prototypes are validation artifacts

**设置：**
- Mechanic concept provided

**输入：** `/prototype wall-jump`

**预期行为：**
1. Skill creates and documents the prototype
2. No director agents are spawned
3. No gate IDs appear in output

**断言：**
- [ ] No director gate is invoked
- [ ] No gate skip messages appear
- [ ] Verdict is PROTOTYPE COMPLETE or PROTOTYPE ABANDONED — no gate verdict

---

## 协议合规性

- [ ] Asks "May I write to `prototypes/[name]/`?" before creating any files
- [ ] Creates all files under `prototypes/` (not `src/`)
- [ ] Produces `findings.md` with tested/worked/didn't-work/recommendation
- [ ] Notes that production coding standards are intentionally relaxed
- [ ] Offers extend/replace/archive when prototype already exists
- [ ] Verdict is PROTOTYPE COMPLETE or PROTOTYPE ABANDONED

---

## 覆盖说明

- Prototype implementation quality (code style) is intentionally not tested —
  prototypes are throwaway artifacts and quality standards do not apply.
- The archiving mechanism is mentioned in Case 2 but the archive format is
  not assertion-tested in detail.
- Engine-specific prototype scaffolding (GDScript scenes vs. C# MonoBehaviour)
  follows the same flow with engine-appropriate file types.

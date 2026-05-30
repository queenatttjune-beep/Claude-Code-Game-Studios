# Skill 测试 Spec: /launch-checklist

## Skill 摘要

`/launch-checklist` generates and evaluates a complete launch readiness checklist
covering: legal compliance (EULA, privacy policy, ESRB/PEGI ratings), platform
certification status, store page completeness (screenshots, description, metadata),
build validation (version tag, reproducible build), analytics and crash reporting
configuration, and first-run experience verification.

The skill produces a checklist report written to `production/launch/launch-checklist-[date].md`
after a "May I write" ask. If a previous launch checklist exists, it compares the
new results against the old to highlight newly resolved and newly blocked items. No
director gates apply — `/team-release` orchestrates the full release pipeline. 裁决：
LAUNCH READY, LAUNCH BLOCKED, or CONCERNS.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: LAUNCH READY, LAUNCH BLOCKED, CONCERNS
- [ ] Contains "May I write" collaborative protocol language before writing the checklist
- [ ] Has a next-step handoff (e.g., `/team-release` or `/day-one-patch`)

---

## Director Gate 检查

无。 `/launch-checklist` is a readiness audit utility. The full release pipeline
is managed by `/team-release`.

---

## 测试用例

### 用例 1: Happy Path — All Checklist Items Verified, LAUNCH READY

**设置：**
- Legal docs present: EULA, privacy policy in `production/legal/`
- Platform certification: marked as submitted and approved in production notes
- Store page assets: screenshots, description, metadata all present in `production/store/`
- Build: version tag `v1.0.0` exists, reproducible build confirmed
- Crash reporting: configured in `technical-preferences.md`

**输入：** `/launch-checklist`

**预期行为：**
1. Skill checks all checklist categories
2. All items pass their verification checks
3. Skill produces checklist report with all items marked PASS
4. Skill asks "May I write to `production/launch/launch-checklist-2026-04-06.md`?"
5. Report written on approval; verdict is LAUNCH READY

**断言：**
- [ ] All checklist categories are checked (legal, platform, store, build, analytics, UX)
- [ ] All items appear in the report with PASS markers
- [ ] Verdict is LAUNCH READY
- [ ] "May I write" is asked with the correct dated filename

---

### 用例 2: Platform Certification Not Submitted — LAUNCH BLOCKED

**设置：**
- All other checklist items pass
- Platform certification section: "not submitted" (no submission record found)

**输入：** `/launch-checklist`

**预期行为：**
1. Skill checks all items
2. Platform certification check fails: no submission record
3. Skill reports: "LAUNCH BLOCKED — Platform certification not submitted"
4. Specific platform(s) missing certification are named
5. Verdict is LAUNCH BLOCKED

**断言：**
- [ ] Verdict is LAUNCH BLOCKED (not CONCERNS)
- [ ] Platform certification is identified as the blocking item
- [ ] Missing platform names are specified
- [ ] All other passing items are still shown in the report

---

### 用例 3: Manual Check Required — CONCERNS Verdict

**设置：**
- All critical checklist items pass
- First-run experience item: "MANUAL CHECK NEEDED — human must play the first 5
  minutes and verify tutorial completion flow"
- Store screenshots item: "MANUAL CHECK NEEDED — art team must verify screenshot
  quality matches current build"

**输入：** `/launch-checklist`

**预期行为：**
1. Skill checks all items
2. 2 items are flagged as requiring human verification
3. Skill reports: "CONCERNS — 2 items require manual verification before launch"
4. Both items are listed with instructions for what to manually verify
5. Verdict is CONCERNS (not LAUNCH BLOCKED, since these are advisory)

**断言：**
- [ ] Verdict is CONCERNS (not LAUNCH READY or LAUNCH BLOCKED)
- [ ] Both manual check items are listed with verification instructions
- [ ] Skill does not auto-block on MANUAL CHECK items

---

### 用例 4: Previous Checklist Exists — Delta Comparison

**设置：**
- `production/launch/launch-checklist-2026-03-25.md` exists with previous results:
  - 2 items were BLOCKED (platform cert, crash reporting)
  - 1 item had a MANUAL CHECK
- New checklist: platform cert is now PASS, crash reporting is now PASS,
  manual check still open; 1 new item flagged (EULA last updated date)

**输入：** `/launch-checklist`

**预期行为：**
1. Skill finds the previous checklist and loads it for comparison
2. Skill produces the new checklist and compares:
   - Newly resolved: "Platform cert — was BLOCKED, now PASS"
   - Newly resolved: "Crash reporting — was BLOCKED, now PASS"
   - Still open: manual check (unchanged)
   - New issue: EULA last updated date (not in previous checklist)
3. Delta is shown prominently in the report
4. Verdict is CONCERNS (manual check + new EULA question)

**断言：**
- [ ] Delta section shows newly resolved items
- [ ] Delta section shows new issues (not present in previous checklist)
- [ ] Still-open items from the previous checklist are noted as persistent
- [ ] Verdict reflects the current state (not the previous state)

---

### 用例 5: Director Gate Check — No gate; launch-checklist is an audit utility

**设置：**
- All checklist dependencies present

**输入：** `/launch-checklist`

**预期行为：**
1. Skill runs the full checklist and writes the report
2. No director agents are spawned
3. No gate IDs appear in output

**断言：**
- [ ] No director gate is invoked
- [ ] No gate skip messages appear
- [ ] Verdict is LAUNCH READY, LAUNCH BLOCKED, or CONCERNS — no gate verdict

---

## 协议合规性

- [ ] Checks all required categories (legal, platform, store, build, analytics, UX)
- [ ] LAUNCH BLOCKED for hard failures (uncompleted certifications, missing legal docs)
- [ ] CONCERNS for advisory items requiring manual verification
- [ ] Compares against previous checklist when one exists
- [ ] Asks "May I write" before creating the checklist report
- [ ] Verdict is LAUNCH READY, LAUNCH BLOCKED, or CONCERNS

---

## 覆盖说明

- Region-specific compliance (GDPR data handling, COPPA for under-13 audiences)
  is checked but the specific requirements are not enumerated in test assertions.
- The store page completeness check (screenshots, description) relies on the
  presence of files in `production/store/`; it cannot verify visual quality.
- Build reproducibility check validates the presence of a version tag and build
  configuration but does not execute the build process.

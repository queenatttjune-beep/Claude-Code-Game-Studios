# Skill 测试 Spec: /regression-suite

## Skill 摘要

`/regression-suite` maps test coverage to GDD requirements: it reads the
acceptance criteria from story files in the current sprint (or a specified epic),
then scans `tests/` for corresponding test files and checks whether each AC has
a matching assertion. It produces a coverage report identifying which ACs are
fully covered, partially covered, or untested, and which test files have no
matching AC (orphan tests).

The skill may write a coverage report to `production/qa/` after a "May I write"
ask. No director gates apply. 裁决： FULL COVERAGE (all ACs have tests),
GAPS FOUND (some ACs are untested), or CRITICAL GAPS (a critical-priority AC
has no test).

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: FULL COVERAGE, GAPS FOUND, CRITICAL GAPS
- [ ] Contains "May I write" language (skill may write coverage report)
- [ ] Has a next-step handoff (e.g., `/test-setup` if framework missing, `/qa-plan` if plan missing)

---

## Director Gate 检查

无。 `/regression-suite` is a QA analysis utility. No director gates apply.

---

## 测试用例

### 用例 1: Full Coverage — All ACs in sprint have corresponding tests

**设置：**
- `production/sprints/sprint-004.md` lists 3 stories with 2 ACs each (6 total)
- `tests/unit/` and `tests/integration/` contain test files that match all 6 ACs
  (by system name and scenario description)

**输入：** `/regression-suite sprint-004`

**预期行为：**
1. Skill reads all 6 ACs from sprint-004 stories
2. Skill scans test files and matches each AC to at least one test assertion
3. All 6 ACs have coverage
4. Skill produces coverage report: "6/6 ACs covered"
5. Skill asks "May I write to `production/qa/regression-sprint-004.md`?"
6. File is written on approval; verdict is FULL COVERAGE

**断言：**
- [ ] All 6 ACs appear in the coverage report
- [ ] Each AC is marked as covered with the matching test file referenced
- [ ] Verdict is FULL COVERAGE
- [ ] "May I write" is asked before writing the report

---

### 用例 2: Gaps Found — 3 ACs have no tests

**设置：**
- Sprint has 5 stories with 8 total ACs
- Tests exist for 5 of the 8 ACs; 3 ACs have no corresponding test file or assertion

**输入：** `/regression-suite`

**预期行为：**
1. Skill reads all 8 ACs
2. Skill scans tests — 5 matched, 3 unmatched
3. Coverage report lists the 3 untested ACs by story and AC text
4. Skill asks "May I write to `production/qa/regression-[sprint]-[date].md`?"
5. Report is written; verdict is GAPS FOUND

**断言：**
- [ ] The 3 untested ACs are listed by name in the report
- [ ] Matched ACs are also shown (not only the gaps)
- [ ] Verdict is GAPS FOUND (not FULL COVERAGE)
- [ ] Report is written after "May I write" approval

---

### 用例 3: Critical AC Untested — CRITICAL GAPS verdict, flagged prominently

**设置：**
- Sprint has 4 stories; one story is Priority: Critical with 2 ACs
- One of the critical-priority ACs has no test

**输入：** `/regression-suite`

**预期行为：**
1. Skill reads all stories and ACs, noting which stories are critical priority
2. Skill scans tests — the critical AC has no match
3. Report prominently flags: "CRITICAL GAP: [AC text] — no test found (Critical priority story)"
4. Skill recommends blocking story completion until test is added
5. Verdict is CRITICAL GAPS

**断言：**
- [ ] Verdict is CRITICAL GAPS (not GAPS FOUND)
- [ ] Critical priority AC is flagged more prominently than normal gaps
- [ ] Recommendation to block story completion is included
- [ ] Non-critical gaps (if any) are also listed

---

### 用例 4: Orphan Tests — Test file has no matching AC

**设置：**
- `tests/unit/save_system_test.gd` exists with assertions for scenarios
  not present in any current story's AC list
- Current sprint stories do not reference save system

**输入：** `/regression-suite`

**预期行为：**
1. Skill scans tests and cross-references ACs
2. `save_system_test.gd` assertions do not match any current AC
3. Test file is flagged as ORPHAN TEST in the coverage report
4. Report notes: "Orphan tests may belong to a past or future sprint, or AC was renamed"
5. Verdict is FULL COVERAGE or GAPS FOUND depending on overall AC coverage
   (orphan tests do not affect verdict, they are advisory)

**断言：**
- [ ] Orphan test is flagged in the report
- [ ] Orphan flag includes the filename and suggestion (past sprint / renamed AC)
- [ ] Orphan tests do not cause a GAPS FOUND verdict on their own
- [ ] Overall verdict reflects AC coverage only

---

### 用例 5: Director Gate Check — No gate; regression-suite is a QA utility

**设置：**
- Sprint with stories and test files

**输入：** `/regression-suite`

**预期行为：**
1. Skill produces coverage report and writes it
2. No director agents are spawned
3. No gate IDs appear in output

**断言：**
- [ ] No director gate is invoked
- [ ] No gate skip messages appear
- [ ] Verdict is FULL COVERAGE, GAPS FOUND, or CRITICAL GAPS — no gate verdict

---

## 协议合规性

- [ ] Reads story ACs from sprint files before scanning tests
- [ ] Matches ACs to tests by system name and scenario (not file name alone)
- [ ] Flags critical-priority untested ACs as CRITICAL GAPS
- [ ] Flags orphan tests (exist in tests/ but no AC matches)
- [ ] Asks "May I write" before persisting the coverage report
- [ ] Verdict is FULL COVERAGE, GAPS FOUND, or CRITICAL GAPS

---

## 覆盖说明

- The heuristic for matching an AC to a test (by system name + scenario keywords)
  is approximate; exact matching logic is defined in the skill body.
- Integration test coverage is mapped the same way as unit test coverage; no
  distinction in verdicts is made between the two.
- This skill does not run the tests — it maps AC text to test assertions. Test
  execution is handled by the CI pipeline.

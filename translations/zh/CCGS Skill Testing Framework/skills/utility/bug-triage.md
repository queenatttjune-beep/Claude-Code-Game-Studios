# Skill 测试 Spec: /bug-triage

## Skill 摘要

`/bug-triage` reads all open bug reports in `production/bugs/` and produces a
prioritized triage table sorted by severity (CRITICAL → HIGH → MEDIUM → LOW).
It runs on the Haiku model (read-only, formatting/sorting task) and produces no
file writes — the triage output is conversational. The skill flags bugs missing
reproduction steps and identifies possible duplicates by comparing titles and
affected systems.

The verdict is always TRIAGED — the skill is advisory and informational. No
director gates apply. The output is intended to help a producer or QA lead
prioritize which bugs to address next.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keyword: TRIAGED
- [ ] Does NOT contain "May I write" language (skill is read-only)
- [ ] Has a next-step handoff (e.g., `/bug-report` to create new reports, `/hotfix` for critical bugs)

---

## Director Gate 检查

无。 `/bug-triage` is a read-only advisory skill. No director gates apply.

---

## 测试用例

### 用例 1: Happy Path — 5 bugs of varying severity, sorted table produced

**设置：**
- `production/bugs/` contains 5 bug report files:
  - bug-2026-03-10-audio-crash.md (CRITICAL)
  - bug-2026-03-12-score-overflow.md (HIGH)
  - bug-2026-03-14-ui-overlap.md (MEDIUM)
  - bug-2026-03-15-typo-tutorial.md (LOW)
  - bug-2026-03-16-vfx-flicker.md (HIGH)

**输入：** `/bug-triage`

**预期行为：**
1. Skill reads all 5 bug report files
2. Skill extracts severity, title, system, and repro status from each
3. Skill produces a triage table sorted: CRITICAL first, then HIGH, MEDIUM, LOW
4. Within the same severity, bugs are ordered by date (oldest first)
5. Verdict is TRIAGED

**断言：**
- [ ] Triage table has exactly 5 rows
- [ ] CRITICAL bug appears before both HIGH bugs
- [ ] HIGH bugs appear before MEDIUM and LOW bugs
- [ ] Verdict is TRIAGED
- [ ] No files are written

---

### 用例 2: No Bug Reports Found — Guidance to run /bug-report

**设置：**
- `production/bugs/` directory exists but is empty (or does not exist)

**输入：** `/bug-triage`

**预期行为：**
1. Skill scans `production/bugs/` and finds no reports
2. Skill outputs: "No open bug reports found in production/bugs/"
3. Skill suggests running `/bug-report` to create a bug report
4. No triage table is produced

**断言：**
- [ ] Output explicitly states no bugs were found
- [ ] `/bug-report` is suggested as the next step
- [ ] Skill does not error out — it handles empty directory gracefully
- [ ] Verdict is TRIAGED (with "no bugs found" context)

---

### 用例 3: Bug Missing Reproduction Steps — Flagged as NEEDS REPRO INFO

**设置：**
- `production/bugs/` contains 3 bug reports; one has an empty "Repro Steps" section

**输入：** `/bug-triage`

**预期行为：**
1. Skill reads all 3 reports
2. Skill detects the report with no repro steps
3. That bug appears in the triage table with a `NEEDS REPRO INFO` tag
4. Other bugs are triaged normally
5. Verdict is TRIAGED

**断言：**
- [ ] `NEEDS REPRO INFO` tag appears next to the bug missing repro steps
- [ ] The flagged bug is still included in the table (not excluded)
- [ ] Other bugs are unaffected
- [ ] Verdict is TRIAGED

---

### 用例 4: Possible Duplicate Bugs — Flagged in triage output

**设置：**
- `production/bugs/` contains 2 bug reports with similar titles:
  - bug-2026-03-18-player-fall-through-floor.md
  - bug-2026-03-20-player-clips-through-floor.md
  - Both affect the "Physics" system with identical severity

**输入：** `/bug-triage`

**预期行为：**
1. Skill reads both reports and detects similar title + same system + same severity
2. Both bugs are included in the triage table
3. Each is tagged with `POSSIBLE DUPLICATE` and cross-references the other report
4. No bugs are merged or deleted — flagging is advisory
5. Verdict is TRIAGED

**断言：**
- [ ] Both bugs appear in the table (not merged)
- [ ] Both are tagged `POSSIBLE DUPLICATE`
- [ ] Each cross-references the other (by filename or title)
- [ ] Verdict is TRIAGED

---

### 用例 5: Director Gate Check — No gate; triage is advisory

**设置：**
- `production/bugs/` contains any number of reports

**输入：** `/bug-triage`

**预期行为：**
1. Skill produces the triage table
2. No director agents are spawned
3. No gate IDs appear in output
4. No write tool is called

**断言：**
- [ ] No director gate is invoked
- [ ] No write tool is called
- [ ] No gate skip messages appear
- [ ] Verdict is TRIAGED without any gate check

---

## 协议合规性

- [ ] Reads all files in `production/bugs/` before generating the table
- [ ] Sorts by severity (CRITICAL → HIGH → MEDIUM → LOW)
- [ ] Flags bugs missing repro steps
- [ ] Flags possible duplicates by title/system similarity
- [ ] Does not write any files
- [ ] Verdict is TRIAGED in all cases (even empty)

---

## 覆盖说明

- The case where a bug report is malformed (missing severity field entirely)
  is not fixture-tested; skill would flag it as `UNKNOWN SEVERITY` and sort it
  last in the table.
- Status transitions (marking bugs as resolved) are outside this skill's scope —
  bug-triage is read-only.
- The duplicate detection heuristic (title similarity + same system) is
  approximate; exact matching logic is defined in the skill body.

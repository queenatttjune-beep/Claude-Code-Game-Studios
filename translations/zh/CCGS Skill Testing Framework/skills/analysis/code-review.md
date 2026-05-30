# Skill 测试 Spec: /code-review

## Skill 摘要

`/code-review` performs an architectural code review of source files in `src/`,
checking coding standards from `CLAUDE.md` (doc comments on public APIs,
dependency injection over singletons, data-driven values, testability). Findings
are advisory. No director gates are invoked. No code edits are made. 裁决：
APPROVED, CONCERNS, or NEEDS CHANGES.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: APPROVED, CONCERNS, NEEDS CHANGES
- [ ] Does NOT require "May I write" language (read-only; findings are advisory output)
- [ ] Has a next-step handoff (what to do with findings)

---

## Director Gate 检查

无。 Code review is a read-only advisory skill; no gates are invoked.

---

## 测试用例

### 用例 1: Happy Path — Source file follows all coding standards

**设置：**
- `src/gameplay/health_component.gd` exists with:
  - All public methods have doc comments (`##` notation)
  - No singletons used; dependencies injected via constructor
  - No hardcoded values; all constants reference `assets/data/`
  - ADR reference in file header: `# ADR: docs/architecture/adr-004-health.md`
  - Referenced ADR has `Status: Accepted`

**输入：** `/code-review src/gameplay/health_component.gd`

**预期行为：**
1. Skill reads the source file
2. Skill checks all coding standards: doc comments, DI, data-driven, ADR status
3. All checks pass
4. Skill outputs findings summary with all checks PASS
5. Verdict is APPROVED

**断言：**
- [ ] Each coding standard check is listed in the output
- [ ] All checks show PASS when standards are met
- [ ] Skill reads referenced ADR to confirm its status
- [ ] Verdict is APPROVED
- [ ] No edits are made to any file

---

### 用例 2: Needs Changes — Missing doc comment and singleton usage

**设置：**
- `src/ui/inventory_ui.gd` has:
  - 2 public methods without doc comments
  - Uses `GameManager.instance` (singleton pattern)
  - All other standards met

**输入：** `/code-review src/ui/inventory_ui.gd`

**预期行为：**
1. Skill reads the source file
2. Skill detects: 2 missing doc comments on public methods
3. Skill detects: singleton usage at specific lines (e.g., line 42, line 87)
4. Findings list the exact method names and line numbers
5. Verdict is NEEDS CHANGES

**断言：**
- [ ] Missing doc comments are listed with method names
- [ ] Singleton usage is flagged with file and line number
- [ ] Verdict is NEEDS CHANGES when BLOCKING-level standard violations exist
- [ ] Skill does not edit the file — findings are for the developer to act on
- [ ] Output suggests replacing singleton with dependency injection

---

### 用例 3: Architecture Risk — ADR reference is Proposed, not Accepted

**设置：**
- `src/core/save_system.gd` has a header comment: `# ADR: docs/architecture/adr-010-save.md`
- `adr-010-save.md` exists but has `Status: Proposed`
- Code itself follows all other coding standards

**输入：** `/code-review src/core/save_system.gd`

**预期行为：**
1. Skill reads the source file
2. Skill reads referenced ADR — finds `Status: Proposed`
3. Skill flags this as ARCHITECTURE RISK (code is implementing an unaccepted ADR)
4. Other coding standard checks pass
5. Verdict is CONCERNS (risk flag is advisory, not a hard NEEDS CHANGES)

**断言：**
- [ ] Skill reads referenced ADR file to check its status
- [ ] ARCHITECTURE RISK is flagged when ADR status is Proposed
- [ ] Verdict is CONCERNS (not NEEDS CHANGES) for ADR risk — advisory severity
- [ ] Output recommends resolving the ADR before the code goes to production

---

### 用例 4: Edge Case — No source files found at specified path

**设置：**
- User calls `/code-review src/networking/`
- `src/networking/` directory does not exist

**输入：** `/code-review src/networking/`

**预期行为：**
1. Skill attempts to read files in `src/networking/`
2. Directory or files not found
3. Skill outputs an error: "No source files found at `src/networking/`"
4. Skill suggests checking `src/` for valid directories
5. No verdict is emitted (nothing was reviewed)

**断言：**
- [ ] Skill does not crash when path does not exist
- [ ] Output names the attempted path in the error message
- [ ] Output suggests checking `src/` for valid file paths
- [ ] No verdict is emitted when there is nothing to review

---

### 用例 5: Gate Compliance — No gate; LP may be consulted separately

**设置：**
- Source file follows most standards but has 1 CONCERNS-level finding (a magic number)
- `review-mode.txt` contains `full`

**输入：** `/code-review src/gameplay/loot_system.gd`

**预期行为：**
1. Skill reads and reviews the source file
2. No director gate is invoked (code review findings are advisory)
3. Skill presents findings with the CONCERNS verdict
4. Output notes: "Consider requesting a Lead Programmer review for architecture concerns"
5. Skill does not invoke any agent automatically

**断言：**
- [ ] No director gate is invoked in any review mode
- [ ] LP consultation is suggested (not mandated) in the output
- [ ] No code edits are made
- [ ] Verdict is CONCERNS for advisory-level findings

---

## 协议合规性

- [ ] Reads source file(s) and coding standards before reviewing
- [ ] Lists each coding standard check in findings output
- [ ] Does not edit any source files (read-only skill)
- [ ] No director gates are invoked
- [ ] Verdict is one of: APPROVED, CONCERNS, NEEDS CHANGES

---

## 覆盖说明

- Batch review of all files in a directory is not explicitly tested; behavior
  is assumed to apply the same checks file by file and aggregate the verdict.
- Test coverage checks (verifying corresponding test files exist) are a stretch
  goal not tested here; that is primarily the domain of `/test-evidence-review`.

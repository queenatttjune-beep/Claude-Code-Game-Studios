# Skill 测试 Spec: /onboard

## Skill 摘要

`/onboard` generates a contextual project onboarding summary tailored for a new
team member. It reads CLAUDE.md, `technical-preferences.md`, the active sprint
file, recent git commits, and `production/stage.txt` to produce a structured
orientation document. The skill runs on the Haiku model (read-only, formatting
task) and produces no file writes — all output is conversational.

The skill optionally accepts a role argument (e.g., `/onboard artist`) to tailor
the summary to a specific discipline. When the project is in an early stage or
unconfigured, the output adapts to reflect what little is known. The verdict is
always ONBOARDING COMPLETE — the skill is purely informational.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keyword: ONBOARDING COMPLETE
- [ ] Does NOT contain "May I write" language (skill is read-only)
- [ ] Has a next-step handoff suggesting a relevant follow-on skill

---

## Director Gate 检查

无。 `/onboard` is a read-only orientation skill. No director gates apply.

---

## 测试用例

### 用例 1: Happy Path — Configured project in Production stage with active sprint

**设置：**
- `production/stage.txt` contains `Production`
- `technical-preferences.md` has engine, language, and specialists populated
- `production/sprints/sprint-005.md` exists with stories in progress
- Git log contains 5 recent commits

**输入：** `/onboard`

**预期行为：**
1. Skill reads stage.txt, technical-preferences.md, active sprint, and git log
2. Skill produces an onboarding summary with sections: Project Overview, Tech Stack,
   Current Stage, Active Sprint Summary, Recent Activity
3. Summary is formatted for readability (headers, bullet points)
4. Next-step suggestions are appropriate for Production stage (e.g., `/sprint-status`,
   `/dev-story`)
5. Verdict ONBOARDING COMPLETE is stated

**断言：**
- [ ] Output includes current stage name from stage.txt
- [ ] Output includes engine and language from technical-preferences.md
- [ ] Active sprint stories are summarized (not just the sprint file name)
- [ ] Recent commit context is present
- [ ] Verdict is ONBOARDING COMPLETE
- [ ] No files are written

---

### 用例 2: Fresh Project — No engine, no sprint, suggests /start

**设置：**
- `technical-preferences.md` contains only placeholders (`[TO BE CONFIGURED]`)
- No `production/stage.txt`
- No sprint files
- No CLAUDE.md overrides beyond defaults

**输入：** `/onboard`

**预期行为：**
1. Skill reads all config files and detects unconfigured state
2. Skill produces a minimal summary: "This project has not been configured yet"
3. Output explains the onboarding workflow: `/start` → `/setup-engine` → `/brainstorm`
4. Skill suggests running `/start` as the immediate next step
5. Verdict is ONBOARDING COMPLETE (informational, not a failure)

**断言：**
- [ ] Output explicitly mentions the project is not yet configured
- [ ] `/start` is recommended as the next step
- [ ] Skill does NOT error out — it gracefully handles an empty project state
- [ ] Verdict is still ONBOARDING COMPLETE

---

### 用例 3: No CLAUDE.md Found — Error with remediation

**设置：**
- `CLAUDE.md` file does not exist (deleted or never created)
- All other files may or may not exist

**输入：** `/onboard`

**预期行为：**
1. Skill attempts to read CLAUDE.md and fails
2. Skill outputs an error: "CLAUDE.md not found — cannot generate onboarding summary"
3. Skill provides remediation: "Run `/start` to initialize the project configuration"
4. No partial summary is generated

**断言：**
- [ ] Error message clearly identifies the missing file as CLAUDE.md
- [ ] Remediation step (`/start`) is explicitly named
- [ ] Skill does NOT produce a partial output when the root config is missing
- [ ] Verdict is ONBOARDING COMPLETE (with error context, not a crash)

---

### 用例 4: Role-Specific Onboarding — User specifies "artist" role

**设置：**
- Fully configured project in Production stage
- `art-bible.md` exists in `design/`
- Active sprint has visual story types (animation, VFX)

**输入：** `/onboard artist`

**预期行为：**
1. Skill reads all standard files plus any art-relevant docs (art bible, asset specs)
2. Summary is tailored to the artist role: art bible overview, asset pipeline,
   current visual stories in the active sprint
3. Technical architecture details (code structure, ADRs) are de-emphasized
4. Specialist agents for art/audio are highlighted in the summary
5. Verdict is ONBOARDING COMPLETE

**断言：**
- [ ] Role argument is acknowledged in the output ("Onboarding for: Artist")
- [ ] Art bible summary is included if the file exists
- [ ] Current visual stories from the active sprint are shown
- [ ] Technical implementation details are not the primary focus
- [ ] Verdict is ONBOARDING COMPLETE

---

### 用例 5: Director Gate Check — No gate; onboard is read-only orientation

**设置：**
- Any configured project state

**输入：** `/onboard`

**预期行为：**
1. Skill completes the full onboarding summary
2. No director agents are spawned at any point
3. No gate IDs appear in the output
4. No "May I write" prompts appear

**断言：**
- [ ] No director gate is invoked
- [ ] No write tool is called
- [ ] No gate skip messages appear
- [ ] Verdict is ONBOARDING COMPLETE without any gate check

---

## 协议合规性

- [ ] Reads all source files before generating output (no hallucinated project state)
- [ ] Adapts output to project stage (Production ≠ Concept)
- [ ] Respects role argument when provided
- [ ] Does not write any files
- [ ] Ends with ONBOARDING COMPLETE verdict in all paths

---

## 覆盖说明

- The case where `technical-preferences.md` is missing entirely (as opposed to
  having placeholders) is not separately tested; behavior follows the graceful
  error pattern of Case 3.
- Git history reading is assumed available; offline/no-git scenarios are not
  tested here.
- Discipline roles beyond "artist" (e.g., programmer, designer, producer) follow
  the same tailoring pattern as Case 4 and are not separately tested.

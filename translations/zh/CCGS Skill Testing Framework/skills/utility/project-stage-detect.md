# Skill 测试 Spec: /project-stage-detect

## Skill 摘要

`/project-stage-detect` automatically analyzes project artifacts to determine
the current development stage. It runs on the Haiku model (read-only) and
examines `production/stage.txt` (if present), design documents in `design/`,
source code in `src/`, sprint and milestone files in `production/`, and the
presence of engine configuration to classify the project into one of seven
stages: Concept, Systems Design, Technical Setup, Pre-Production, Production,
Polish, or Release.

The skill is advisory — it never writes `stage.txt`. That file is only updated
when `/gate-check` passes and the user confirms advancement. The skill reports
its confidence level (HIGH if stage.txt was read directly, MEDIUM if inferred
from artifacts, LOW if conflicting signals were found).

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains all seven stage names: Concept, Systems Design, Technical Setup, Pre-Production, Production, Polish, Release
- [ ] Does NOT contain "May I write" language (skill is detection-only)
- [ ] Has a next-step handoff (e.g., `/gate-check` to formally advance stage)

---

## Director Gate 检查

无。 `/project-stage-detect` is a read-only detection utility. No director
gates apply.

---

## 测试用例

### 用例 1: stage.txt Exists — Reads directly and cross-checks artifacts

**设置：**
- `production/stage.txt` contains `Production`
- `design/gdd/` has 4 GDD files
- `src/` has source code files
- `production/sprints/sprint-002.md` exists

**输入：** `/project-stage-detect`

**预期行为：**
1. Skill reads `production/stage.txt` — detects stage `Production`
2. Skill cross-checks artifacts: GDDs present, source code present, sprint present
3. Artifacts are consistent with Production stage
4. Skill reports: Stage = Production, Confidence = HIGH (from stage.txt, confirmed by artifacts)
5. Next step: continue with `/sprint-plan` or `/dev-story`

**断言：**
- [ ] Detected stage is Production
- [ ] Confidence is reported as HIGH when stage.txt is present
- [ ] Cross-check result (consistent vs. discrepant) is noted
- [ ] No files are written
- [ ] Verdict clearly states the detected stage

---

### 用例 2: No stage.txt but GDDs and Epics Exist — Infers Production

**设置：**
- No `production/stage.txt`
- `design/gdd/` has 3 GDD files
- `production/epics/` has 2 epic files
- `src/` has source code files
- `production/sprints/sprint-001.md` exists

**输入：** `/project-stage-detect`

**预期行为：**
1. Skill finds no stage.txt — switches to artifact inference mode
2. Skill finds GDDs (Systems Design complete), epics (Pre-Production complete),
   source code and sprints (Production active)
3. Skill infers: Stage = Production
4. Confidence is MEDIUM (inferred from artifacts, not from stage.txt)
5. Skill recommends running `/gate-check` to formalize and write stage.txt

**断言：**
- [ ] Inferred stage is Production
- [ ] Confidence is MEDIUM (not HIGH, since stage.txt is absent)
- [ ] Recommendation to run `/gate-check` is present
- [ ] No stage.txt is written by this skill

---

### 用例 3: No stage.txt, No Docs, No Source — Infers Concept

**设置：**
- No `production/stage.txt`
- `design/` directory exists but is empty
- `src/` exists but contains no code files
- `technical-preferences.md` has placeholders only

**输入：** `/project-stage-detect`

**预期行为：**
1. Skill finds no stage.txt
2. Artifact scan: no GDDs, no source, no epics, no sprints, engine unconfigured
3. Skill infers: Stage = Concept
4. Confidence is MEDIUM
5. Skill suggests `/start` to begin the onboarding workflow

**断言：**
- [ ] Inferred stage is Concept
- [ ] Output lists the artifacts that were checked (and found absent)
- [ ] `/start` is suggested as the next step
- [ ] No files are written

---

### 用例 4: Discrepancy — stage.txt says Production but no source code

**设置：**
- `production/stage.txt` contains `Production`
- `design/gdd/` has GDD files
- `src/` directory exists but contains no source code files
- No sprint files exist

**输入：** `/project-stage-detect`

**预期行为：**
1. Skill reads stage.txt — detects `Production`
2. Cross-check finds: no source code, no sprints — inconsistent with Production
3. Skill flags discrepancy: "stage.txt says Production but no source code or sprints found"
4. Skill reports detected stage as Production (honoring stage.txt) but
   confidence drops to LOW due to artifact mismatch
5. Skill suggests reviewing stage.txt manually or running `/gate-check`

**断言：**
- [ ] Discrepancy is flagged explicitly in the output
- [ ] Confidence is LOW when artifacts contradict stage.txt
- [ ] stage.txt value is not silently overridden
- [ ] User is advised to verify the discrepancy manually

---

### 用例 5: Director Gate Check — No gate; detection is advisory

**设置：**
- Any project state with or without stage.txt

**输入：** `/project-stage-detect`

**预期行为：**
1. Skill completes full stage detection
2. No director agents are spawned at any point
3. No gate IDs appear in output
4. No write tool is called

**断言：**
- [ ] No director gate is invoked
- [ ] No write tool is called
- [ ] Detection output is purely advisory
- [ ] Verdict names the detected stage without triggering any gate

---

## 协议合规性

- [ ] Reads stage.txt if present; falls back to artifact inference if absent
- [ ] Always reports a confidence level (HIGH / MEDIUM / LOW)
- [ ] Cross-checks stage.txt against artifacts and flags discrepancies
- [ ] Does not write stage.txt (that is `/gate-check`'s responsibility)
- [ ] Ends with a next-step recommendation appropriate to the detected stage

---

## 覆盖说明

- The Technical Setup stage (engine configured, no GDDs yet) and Pre-Production
  stage (GDDs complete, no epics yet) follow the same artifact-inference pattern
  as Cases 2 and 3 and are not separately fixture-tested.
- The Polish and Release stages are not fixture-tested here; they follow the
  same high-confidence (stage.txt present) or inference logic.
- Confidence levels are advisory — the skill does not gate any actions on them.

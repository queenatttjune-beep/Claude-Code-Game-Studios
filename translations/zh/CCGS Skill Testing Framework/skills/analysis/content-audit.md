# Skill 测试 Spec: /content-audit

## Skill 摘要

`/content-audit` reads GDDs in `design/gdd/` and checks whether all content
items specified there (enemies, items, levels, etc.) are accounted for in
`assets/`. It produces a gap table: Content Type → Specified Count → Found Count
→ Missing Items. No director gates are invoked. The skill does not write without
user approval. 裁决： COMPLETE, GAPS FOUND, or MISSING CRITICAL CONTENT.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keywords: COMPLETE, GAPS FOUND, MISSING CRITICAL CONTENT
- [ ] Does NOT require "May I write" language (read-only output; write is optional report)
- [ ] Has a next-step handoff (what to do after gap table is reviewed)

---

## Director Gate 检查

无。 Content audit is a read-only analysis skill; no gates are invoked.

---

## 测试用例

### 用例 1: Happy Path — All specified content present

**设置：**
- `design/gdd/enemies.md` specifies 4 enemy types: Grunt, Sniper, Tank, Boss
- `assets/art/characters/` contains folders: `grunt/`, `sniper/`, `tank/`, `boss/`
- `design/gdd/items.md` specifies 3 item types; all 3 found in `assets/data/items/`

**输入：** `/content-audit`

**预期行为：**
1. Skill reads all GDDs in `design/gdd/`
2. Skill scans `assets/` for each specified content item
3. All 4 enemy types and 3 item types are found
4. Gap table shows: all rows have Found Count = Specified Count, no missing items
5. Verdict is COMPLETE

**断言：**
- [ ] Gap table covers all content types found in GDDs
- [ ] Each row shows Specified Count and Found Count
- [ ] No missing items when counts match
- [ ] Verdict is COMPLETE
- [ ] No files are written

---

### 用例 2: Gaps Found — Enemy type missing from assets

**设置：**
- `design/gdd/enemies.md` specifies 3 enemy types: Grunt, Sniper, Boss
- `assets/art/characters/` contains: `grunt/`, `sniper/` only (Boss folder missing)

**输入：** `/content-audit`

**预期行为：**
1. Skill reads GDD — finds 3 enemy types specified
2. Skill scans `assets/art/characters/` — finds only 2
3. Gap table row for enemies: Specified 3, Found 2, Missing: Boss
4. Verdict is GAPS FOUND

**断言：**
- [ ] Gap table row identifies "Boss" as the missing item by name
- [ ] Specified Count (3) and Found Count (2) are both shown
- [ ] Verdict is GAPS FOUND when any content item is missing
- [ ] Skill does not assume the asset will be added later — it flags it now

---

### 用例 3: No GDD Content Specs Found — Guidance given

**设置：**
- `design/gdd/` contains only `core-loop.md` which has no content inventory section
- No other GDDs exist with content specifications

**输入：** `/content-audit`

**预期行为：**
1. Skill reads all GDDs — finds no content inventory sections
2. Skill outputs: "No content specifications found in GDDs — run /design-system first to define content lists"
3. No gap table is produced
4. Verdict is GAPS FOUND (cannot confirm completeness without specs)

**断言：**
- [ ] Skill does not produce a gap table when no GDD content specs exist
- [ ] Output recommends running `/design-system`
- [ ] Verdict reflects inability to confirm completeness

---

### 用例 4: Edge Case — Asset in wrong format for target platform

**设置：**
- `design/gdd/audio.md` specifies audio assets as OGG format
- `assets/audio/sfx/jump.wav` exists (WAV format, not OGG)
- `assets/audio/sfx/land.ogg` exists (correct format)
- `technical-preferences.md` specifies audio format: OGG

**输入：** `/content-audit`

**预期行为：**
1. Skill reads GDD audio spec and technical preferences for format requirements
2. Skill finds `jump.wav` — present but in wrong format
3. Gap table row for audio: Specified 2, Found 2 (by name), but `jump.wav` flagged as FORMAT ISSUE
4. Verdict is GAPS FOUND (format compliance is part of content completeness)

**断言：**
- [ ] Skill checks asset format against GDD or technical preferences when format is specified
- [ ] `jump.wav` is flagged as FORMAT ISSUE with expected format (OGG) noted
- [ ] Format issues are distinct from missing content in the gap table
- [ ] Verdict is GAPS FOUND when format issues exist

---

### 用例 5: Gate Compliance — Read-only; no gate; gap table for human review

**设置：**
- GDDs specify 10 content items; 9 are found in assets; 1 is missing
- `review-mode.txt` contains `full`

**输入：** `/content-audit`

**预期行为：**
1. Skill reads GDDs and scans assets; produces gap table
2. No director gate is invoked regardless of review mode
3. Skill presents gap table to user as read-only output
4. Verdict is GAPS FOUND
5. Skill offers to write an audit report but does not write automatically

**断言：**
- [ ] No director gate is invoked in any review mode
- [ ] Gap table is presented without auto-writing any file
- [ ] Optional report write is offered but not forced
- [ ] Skill does not modify any asset files

---

## 协议合规性

- [ ] Reads GDDs and asset directory before producing gap table
- [ ] Gap table shows Content Type, Specified Count, Found Count, Missing Items
- [ ] Does not write files without explicit user approval
- [ ] No director gates are invoked
- [ ] Verdict is one of: COMPLETE, GAPS FOUND, MISSING CRITICAL CONTENT

---

## 覆盖说明

- MISSING CRITICAL CONTENT verdict (vs. GAPS FOUND) is triggered when the
  missing item is tagged as critical in the GDD; this is not explicitly tested
  but follows the same detection path.
- The case where `assets/` directory does not exist is not tested; the skill
  would produce a MISSING CRITICAL CONTENT verdict for all specified items.

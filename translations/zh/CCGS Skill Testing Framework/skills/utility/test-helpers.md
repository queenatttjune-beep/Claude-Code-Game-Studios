# Skill 测试 Spec: /test-helpers

## Skill 摘要

`/test-helpers` generates engine-specific test helper utilities for the project's
test suite. Helpers include factory functions (for creating test entities with
known state), fixture loaders, assertion helpers, and mock stubs for external
dependencies. Generated helpers follow the naming and structure conventions in
`coding-standards.md` and are written to `tests/helpers/`.

Each helper file is gated behind a "May I write" ask. If a helper file already
exists, the skill offers to extend it rather than replace. No director gates
apply. The verdict is COMPLETE when helper files are written.

---

## 静态断言（结构性）

Verified automatically by `/skill-test static` — no fixture needed.

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings
- [ ] Contains verdict keyword: COMPLETE
- [ ] Contains "May I write" collaborative protocol language before writing helpers
- [ ] Has a next-step handoff (e.g., write a test using the generated helper)

---

## Director Gate 检查

无。 `/test-helpers` is a scaffolding utility. No director gates apply.

---

## 测试用例

### 用例 1: Happy Path — Player factory helper generated for Godot/GDScript

**设置：**
- `technical-preferences.md` has engine Godot 4, language GDScript
- `tests/` directory exists (test-setup has been run)
- `design/gdd/player.md` exists with defined player properties
- No existing helpers in `tests/helpers/`

**输入：** `/test-helpers player-factory`

**预期行为：**
1. Skill reads engine (Godot 4 / GDScript) and player GDD for property context
2. Skill generates a deterministic `PlayerFactory` helper in GDScript:
   - `create_player(health: int = 100, speed: float = 200.0)` function
   - Returns a player node pre-configured to a known state
   - Uses dependency injection (no singletons)
3. Skill asks "May I write to `tests/helpers/player_factory.gd`?"
4. File is written on approval; verdict is COMPLETE

**断言：**
- [ ] Generated helper is in GDScript (not C# or Blueprint)
- [ ] Factory function parameters use defaults matching GDD values
- [ ] Helper uses dependency injection (no Autoload/singleton references)
- [ ] Filename follows snake_case convention for GDScript
- [ ] Verdict is COMPLETE

---

### 用例 2: No Test Setup Exists — Redirects to /test-setup

**设置：**
- `tests/` directory does not exist

**输入：** `/test-helpers player-factory`

**预期行为：**
1. Skill checks for `tests/` directory — not found
2. Skill reports: "Test directory not found — test framework must be set up first"
3. Skill suggests running `/test-setup` before generating helpers
4. No helper file is created

**断言：**
- [ ] Error message identifies the missing tests/ directory
- [ ] `/test-setup` is suggested as the prerequisite step
- [ ] No write tool is called
- [ ] Verdict is not COMPLETE (blocked state)

---

### 用例 3: Helper Already Exists — Offers to extend rather than replace

**设置：**
- `tests/helpers/player_factory.gd` already exists with a `create_player()` function
- User requests a new `create_enemy()` function be added to the factory

**输入：** `/test-helpers enemy-factory`

**预期行为：**
1. Skill finds an existing `player_factory.gd` and checks if it's the right file
   to extend (or if a separate `enemy_factory.gd` should be created)
2. Skill presents options: add `create_enemy()` to existing factory or create
   `tests/helpers/enemy_factory.gd`
3. User selects extend; skill drafts the `create_enemy()` function
4. Skill asks "May I extend `tests/helpers/player_factory.gd`?"
5. Function is added on approval; verdict is COMPLETE

**断言：**
- [ ] Existing helper is detected and surfaced
- [ ] User is given extend vs. new file choice
- [ ] "May I extend" language is used (not "May I write" for replacement)
- [ ] Existing `create_player()` is preserved in the extended file
- [ ] Verdict is COMPLETE

---

### 用例 4: System Has No GDD — Notes missing design context in helper

**设置：**
- `technical-preferences.md` has Godot 4 / GDScript
- `tests/` exists
- User requests a helper for the "inventory system" but no `design/gdd/inventory.md` exists

**输入：** `/test-helpers inventory-factory`

**预期行为：**
1. Skill looks for `design/gdd/inventory.md` — not found
2. Skill notes: "No GDD found for inventory — generating helper with placeholder defaults"
3. Skill generates an `inventory_factory.gd` with generic placeholder values
   (item_count = 0, max_capacity = 20) and a comment: "# TODO: align defaults
   with inventory GDD when written"
4. Skill asks "May I write to `tests/helpers/inventory_factory.gd`?"
5. File is written; verdict is COMPLETE with advisory note

**断言：**
- [ ] Skill proceeds without GDD (does not block)
- [ ] Generated helper has placeholder defaults with TODO comment
- [ ] Missing GDD is noted in the output (advisory warning)
- [ ] Verdict is COMPLETE

---

### 用例 5: Director Gate Check — No gate; test-helpers is a scaffolding utility

**设置：**
- Engine configured, tests/ exists

**输入：** `/test-helpers player-factory`

**预期行为：**
1. Skill generates and writes the helper file
2. No director agents are spawned
3. No gate IDs appear in output

**断言：**
- [ ] No director gate is invoked
- [ ] No gate skip messages appear
- [ ] Verdict is COMPLETE without any gate check

---

## 协议合规性

- [ ] Reads engine before generating any helper (helpers are engine-specific)
- [ ] Reads GDD for default values when available
- [ ] Notes missing GDD context rather than blocking
- [ ] Detects existing helper files and offers extend rather than replace
- [ ] Asks "May I write" (or "May I extend") before any file operation
- [ ] Verdict is COMPLETE when helper is written

---

## 覆盖说明

- Mock/stub helper generation (for dependencies like save systems or audio buses)
  follows the same pattern as factory helpers and is not separately tested.
- Unity C# helper generation (using NSubstitute or custom mocks) follows the
  same logic as Case 1 with language-appropriate output.
- The case where the requested helper type is not recognized is not tested;
  the skill would ask the user to clarify the helper type.

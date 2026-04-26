# Server.cfg Style Standards (Draft)

## Scope
This draft defines style and section-order standards for .cfg files that follow server.cfg conventions in this repository.

Primary references used for this draft:
- [template/server.cfg.template](template/server.cfg.template#L1)
- [csgo/server.cfg](csgo/server.cfg#L1)
- [q3/server.cfg](q3/server.cfg#L1)
- [cod4/server.cfg](cod4/server.cfg#L1)

Additional pilot samples used to validate cross-family behavior:
- [ins/server.cfg](ins/server.cfg#L1)
- [cs/server.cfg](cs/server.cfg#L1)
- [jk2/server.cfg](jk2/server.cfg#L1)

## Style Rules

### 1) File Preamble
- Use a visible file header block at top of file.
- Header must include game name, file name, and version/date.
- Prefer the template border style from [template/server.cfg.template](template/server.cfg.template#L1).
- Keep one blank line after header.

### 2) Section Headings
- Group settings in named sections.
- Preferred heading style:
  - // ............................. Section Name ............................ //
- Use one blank line before each section heading.
- Keep heading names short, stable, and semantic (Basic, Logging, Network, Security, Gameplay, Rotation, Exec).

### 3) Setting Documentation
- Each configurable setting should be documented immediately above the setting.
- Preferred description line format:
  - // cvar_name - Short purpose statement.
- Optional lines:
  - // Default: cvar_name value
  - // Recommended: cvar_name value
  - // Example: cvar_name "value"
- Keep comments objective and avoid conversational phrasing.

### 4) Assignment Style
- Use the native command style of the family and do not mix styles in one file:
  - Source/GoldSrc: direct cvar assignment (example: sv_lan 0)
  - id Tech/Quake/Jedi: set or seta (example: set sv_hostname "SERVERNAME")
  - CoD/CoD4X: set or sets for metadata
- One setting per line.
- Inline comments are allowed only when concise and not a substitute for full setting docs.

### 5) Quoting and Value Conventions
- Quote string values.
- Do not quote numeric values unless the engine style in that family consistently uses quotes.
- Keep placeholders uppercase for user-provided secrets or names (SERVERNAME, ADMINPASSWORD).

### 6) Spacing and Layout
- Keep one blank line between logical setting groups.
- Avoid repeated blank lines.
- No trailing whitespace.
- Keep lines readable; long map rotations may remain on one line only when engine syntax requires it.

### 7) Includes and Runtime Commands
- Keep includes/exec directives in a dedicated Exec or Ban section near end of configuration sections.
- Final startup actions (for example map or rotation start commands) should be placed in final section.

### 8) Comment Language and Tone
- Use clear technical English.
- Avoid jokes, uncertainty comments, or subjective wording in standards-conforming files.

## Recommended Baseline Cvars to Add to the Standard
These are high-value cvars that should be explicitly tracked in the standard, but are not currently required as a baseline list.

### Source and GoldSrc Baseline Candidates
- `sv_setsteamaccount` - Required for modern server token registration on many Source deployments.
- `sv_pure` (or `mp_consistency` for older GoldSrc variants) - Asset consistency and anti-cheat hardening.
- `sv_minrate`, `sv_maxrate` - Network throughput guardrails.
- `sv_minupdaterate`, `sv_maxupdaterate` - Tick/update policy consistency.
- `sv_mincmdrate`, `sv_maxcmdrate` - Client command rate policy consistency.
- `sv_timeout` - Connection timeout stability baseline.
- `sv_contact` - Admin contact traceability.
- `logaddress_add` (if remote logging is used) - Centralized moderation/audit logging.
- `sv_tags` - Discoverability and server browser clarity.

### Cross-Family Candidates
- `sv_region` (or family equivalent) - Predictable regional listing.
- Explicit startup command (`map`, `map_rotate`, `vstr`, or family equivalent) - deterministic boot behavior.
- Explicit ban include/write commands (`exec banned*.cfg`, `writeid`, `writeip`, or family equivalent) - ban persistence.

Note: Not all candidates apply to every engine family. Standards should tag each cvar as `required`, `recommended`, or `not-applicable` per family.

## Style Conformance Checklist
Use this checklist for each file.

- [ ] Header block exists and contains game + file + version/date.
- [ ] Section headings are present and consistently formatted.
- [ ] Every critical setting has a preceding purpose comment.
- [ ] Default/recommended/example lines are used consistently where needed.
- [ ] Assignment style is consistent with config family.
- [ ] Quote usage is consistent with family norms.
- [ ] Blank-line spacing is consistent and minimal.
- [ ] Includes/exec commands grouped in a dedicated section.
- [ ] Startup commands (map/map_rotate/vstr) located at end section.
- [ ] Comment tone is technical and neutral.

## Section-Order Models by Config Family

### Model A: Source and GoldSrc Family
Applies to files like [csgo/server.cfg](csgo/server.cfg#L1), [cs/server.cfg](cs/server.cfg#L1), [ins/server.cfg](ins/server.cfg#L1).

Recommended order:
1. Basic identity and access (hostname, rcon, server password, contact, region)
2. Security and trust (sv_lan, sv_cheats, anti-cheat, upload/download)
3. Logging and diagnostics
4. Network and rates
5. Voice/chat/social rules
6. Gameplay and mode rules
7. Matchmaking/playlists and mapcycle
8. Voting/admin controls
9. Banlist and exec include files
10. Startup map/rotation trigger

### Model B: id Tech and Quake-Derived Family
Applies to files like [q3/server.cfg](q3/server.cfg#L1), [jk2/server.cfg](jk2/server.cfg#L1).

Recommended order:
1. Server identity and access
2. Master server registration and visibility
3. Network and player limits
4. Core gameplay limits (timelimit, fraglimit, gametype)
5. Advanced gameplay cvars
6. Logging
7. Map rotation variable chain
8. Final rotation start command (vstr or map)

### Model C: CoD and CoD4X Family
Applies to files like [cod4/server.cfg](cod4/server.cfg#L1).

Recommended order:
1. Metadata (sets _Admin, _Website, etc.)
2. Basics (hostname, dedicated, passwords)
3. Logging/status files
4. Networking and connection controls
5. Protected or fixed engine variables section
6. Gameplay shared settings
7. Per-gamemode settings blocks
8. Custom download/mod support
9. Steam/platform toggles
10. Startup map command (map_rotate or map)

## Section-Order Checklist
Use this checklist to audit ordering independent of style details.

- [ ] File has identifiable sections.
- [ ] Section sequence matches one family model (A, B, or C).
- [ ] Identity/access appears before gameplay details.
- [ ] Logging appears before or near network diagnostics.
- [ ] Map cycle definitions appear near end of file.
- [ ] Final startup command appears in final section.
- [ ] Ban/exec includes appear in dedicated near-end section.
- [ ] No major section appears twice unless intentionally split and labeled.

## Pilot Audit (No Bulk Edits Applied)

### Pilot Coverage
- Template baseline: [template/server.cfg.template](template/server.cfg.template#L1)
- Source family sample: [csgo/server.cfg](csgo/server.cfg#L1)
- id Tech sample: [q3/server.cfg](q3/server.cfg#L1)
- CoD sample: [cod4/server.cfg](cod4/server.cfg#L1)
- Extra checks: [ins/server.cfg](ins/server.cfg#L1), [jk2/server.cfg](jk2/server.cfg#L1), [cs/server.cfg](cs/server.cfg#L1)

### Style Violations Found

1. [csgo/server.cfg](csgo/server.cfg#L1)
- Mixed heading widths and punctuation style.
- Some settings are grouped without full comment metadata (for example hibernation/query block).
- No explicit startup map/rotation terminal section.

2. [q3/server.cfg](q3/server.cfg#L1)
- Missing standardized header block and explicit section headings.
- Frequent inline comments instead of preceding documentation lines.
- Section boundaries implied, not explicit.

3. [cod4/server.cfg](cod4/server.cfg#L1)
- Comment tone includes subjective and conversational text.
- Section separators are inconsistent (different slash and hash divider styles).
- Very large gameplay block with weak sub-section boundaries.

4. [jk2/server.cfg](jk2/server.cfg#L1)
- Non-standard top heading style and mixed separator conventions.
- Post-line comments often placed below with uneven style.
- Mixed density of documentation across related cvars.

5. [cs/server.cfg](cs/server.cfg#L1)
- Minimal sectioning and partial comment coverage.
- Logging/admin sections not isolated as dedicated named sections.

6. [ins/server.cfg](ins/server.cfg#L1)
- Mostly conforms, but header border and heading density differ from template style.
- Very long section with many voting settings would benefit from sub-sections.

### Ordering Audit Results

1. [csgo/server.cfg](csgo/server.cfg#L1)
- Family model: A (Source/GoldSrc)
- Result: Partial pass
- Ordering violations:
  - Ban list appears early relative to broader gameplay/network model.
  - No explicit terminal startup trigger section.

2. [q3/server.cfg](q3/server.cfg#L1)
- Family model: B (id Tech)
- Result: Partial pass
- Ordering violations:
  - No explicit section order markers, making audit and maintenance harder.
  - Logging section absent as explicit block.

3. [cod4/server.cfg](cod4/server.cfg#L1)
- Family model: C (CoD)
- Result: Pass with structural risk
- Ordering violations:
  - Protected engine variables mixed into broader stream without strict section identity.
  - Startup and platform toggles are near end but not wrapped in a final startup section.

4. [ins/server.cfg](ins/server.cfg#L1)
- Family model: A (Source/GoldSrc)
- Result: Pass
- Ordering note:
  - Strong sectioning already present; only normalization needed.

## Proposed Fix Batches (Before Any Bulk Edits)

Batch 1: Header and section normalization
- Add template-style file header and stable section heading format.
- Target first: [q3/server.cfg](q3/server.cfg#L1), [jk2/server.cfg](jk2/server.cfg#L1), [cs/server.cfg](cs/server.cfg#L1).
- Risk: low.

Batch 2: Comment and setting doc normalization
- Convert conversational/mixed comments to neutral format.
- Add missing cvar purpose lines for critical settings.
- Target first: [cod4/server.cfg](cod4/server.cfg#L1), [csgo/server.cfg](csgo/server.cfg#L1).
- Risk: low to medium (human review needed to preserve intent).

Batch 3: Section-order realignment by family model
- Reorder blocks to Model A/B/C while preserving values.
- Introduce explicit final startup section.
- Target first: [q3/server.cfg](q3/server.cfg#L1), [cs/server.cfg](cs/server.cfg#L1), [csgo/server.cfg](csgo/server.cfg#L1).
- Risk: medium (ordering can impact load behavior if commands have dependencies).

Batch 4: Large-block decomposition
- Split long gameplay and vote blocks into labeled sub-sections.
- Target first: [cod4/server.cfg](cod4/server.cfg#L1), [ins/server.cfg](ins/server.cfg#L1).
- Risk: medium (requires careful grouping review).

## Notes
- This is a draft standard for review.
- No bulk edits were applied to configuration files in this pass.

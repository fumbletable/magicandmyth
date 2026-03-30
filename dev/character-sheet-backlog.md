---
title: Character Sheet Backlog
layout: default
parent: Dev Tools
nav_order: 2
---

# Character Sheet Backlog

Outstanding improvements for the character generator, identified during the 2026-03-30 audit.

## Completed (2026-03-30)

For reference, these were fixed in commits `709fa3f`, `ca58f03`, and `f7f60e2`:

- All 10 class saving throws verified and corrected against rules markdown
- Fighter BTH corrected to +1 (was defaulting to 0)
- Barbarian BTH corrected to +0 (JSON had 1)
- NWP starting slots fixed: barbarian (3->2), cleric (3->4), druid (3->4), monk (3->4)
- Fighter NWP groups: added missing Nature
- Barbarian weapon groups: was "all", now specific groups per rules
- Barbarian armor text: was "Any", now restricted per rules
- Monk allowed_ancestries: expanded to all per rules text
- Bard attribute requirements: added INT 12 / CHA 14
- Alignment selection step added
- Ancestry free weapon proficiency choices (elf, dwarf, halfling, half-elf, half-orc, gnome)
- Ancestry free NWP choices (dwarf, gnome, half-orc)
- Class free NWPs auto-added (cleric, bard, druid, ranger, thief, wizard)
- Class talent selection step with Level 1 filtering

---

## Remaining Fixes

### Half-Elf Ambitious Start (WP or NWP choice)

**Priority:** Medium
**Effort:** Small

The half-elf trait "Ambitious Start" says: "One additional proficiency slot at character creation (nonweapon **or** weapon)." Currently the code only adds +1 to NWP slots. Should present a choice during the ancestry proficiencies step.

**Files:** `dev/character-generator.md` (showAncestryProfsStep and NWP/WP slot calculation)

---

### Attack Rate Display

**Priority:** Medium
**Effort:** Small

The rules define attack rates per class per level (1/1, 3/2, 2/1, etc.) but the character sheet doesn't display this. At Level 1 all classes are 1/1, so this only matters for higher levels — but since the sheet supports levelling up via editable fields, it should be shown.

**Approach:** Add `attack_rate` to each level in the class level charts, or derive from BTH/level. Display in the Combat section next to BTH.

**Files:** `data/classes.json` (add attack_rate data), `dev/character-generator.md` (renderSheet combat section)

---

### Small Race Weapon Damage Reduction

**Priority:** Low
**Effort:** Medium

Gnomes and halflings have: "Weapons deal one less dice category in damage (e.g., 1d8 becomes 1d6)." The generator doesn't reduce weapon damage dice for small ancestries. The weapon table on the sheet shows standard damage.

**Approach:** Add a `size` field to ancestry data ("Small" for gnome/halfling). When rendering the weapon table, if ancestry is Small, step down damage dice (d12->d10, d10->d8, d8->d6, d6->d4, d4->d3, d3->d2).

**Files:** `data/ancestries.json`, `dev/character-generator.md` (weapon table rendering)

---

### Wizard Starting Spellbook Prompt

**Priority:** Medium
**Effort:** Medium

Rules say a wizard starts with "a number of 1st-level spells equal to half your Intelligence score." The generator doesn't prompt for initial spellbook selection during creation. Players can add spells post-creation on the sheet, but the starting count isn't enforced or guided.

**Approach:** After the name step (or as a sub-step), if class is wizard, show the arcane Level 1 spell list and let the player pick up to floor(INT/2) spells for their starting spellbook.

**Files:** `dev/character-generator.md` (add step after name or before equipment)

---

### Barbarian Wilderness Skills Rogue Proficiency Choice

**Priority:** Low
**Effort:** Small

The barbarian "Wilderness Skills" ability grants Minor proficiency in Wilderness Survival (already added as a class free NWP) **plus one bonus rogue proficiency** from: Acrobatics, Animal Handling, Climbing, Set Snares, or Tracking. This bonus choice isn't presented.

**Approach:** Add a choice step or auto-present during the NWP step. Could be handled similar to ancestry free NWP choices.

**Files:** `dev/character-generator.md` (CLASS_FREE_NWPS barbarian entry, showNWPStep)

---

### Barbarian Level Chart in JSON

**Priority:** Low
**Effort:** Small (cleanup)

The old `level_chart` array in the barbarian entry of `classes.json` had completely wrong data (wrong XP, wrong specials like "Feral Senses, Rage 1/day" that don't exist in the rules). It's no longer used for BTH (we added `bth_level1`), but the stale data should be removed to avoid confusion.

**Approach:** Either remove the `level_chart` from barbarian entirely, or replace it with correct data from the rules markdown.

**Files:** `data/classes.json`

---

### Multi-Class Support

**Priority:** Low
**Effort:** Large

The character creation rules mention multi-classing as an option ("Choose one or a combination of two if multi-classing"). Not currently supported. This would require:

- Dual class selection with attribute requirement checks
- Split XP tracking
- Combined save/BTH calculations
- Merged proficiency group access
- Hit die averaging

This is a significant feature and probably not worth building unless players actively want it.

**Files:** Everything

---

### NWP Event Listener Leak

**Priority:** Low
**Effort:** Small (bug fix)

In `showNWPStep()`, the event delegation listener uses `{ once: true }` which means only the first click in the NWP grid registers. If a player clicks a proficiency and then wants to click another without re-rendering, the second click won't work. The step re-renders after each pick (which re-attaches), so it mostly works, but it's fragile.

**Approach:** Remove `{ once: true }` or use a persistent delegated handler like the WP step does.

**Files:** `dev/character-generator.md` (showNWPStep event listener)

---

### Druid/Ranger Spell Sphere Access

**Priority:** Low
**Effort:** Medium

The spellcasting section on the sheet shows "divine caster" but doesn't indicate which spell spheres the class has access to (major vs minor). Druids and clerics have different sphere access. This matters when preparing spells at higher levels.

**Approach:** Add sphere access data to the class spellcasting config and display it in the spellcasting section header.

**Files:** `data/classes.json` (spellcasting.spheres), `dev/character-generator.md` (spell section rendering)

---

### Cleric Deity / Mythos Selection

**Priority:** Low
**Effort:** Medium

Cleric alignment depends on deity ("within one step of deity's alignment"). Currently all alignments are shown. If deity data exists or is added, the alignment options could be filtered. Also affects weapon restrictions ("Weapon restrictions vary by mythos").

**Approach:** Would need a deity/mythos data source. Probably best handled as a text field for now.

---

### Editable Alignment on Sheet

**Priority:** Low
**Effort:** Small

Alignment is displayed on the sheet but isn't editable (no `editable` class). Should be clickable to change, in case a player needs to correct it or their alignment shifts during play.

**Files:** `dev/character-generator.md` (renderSheet header section)

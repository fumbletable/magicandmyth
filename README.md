# Magic & Myth

Companion site and digital tools for the Magic & Myth tabletop RPG.

## Structure

```
data/           — Structured JSON game data (single source of truth)
site/           — Quartz companion site (GitHub Pages)
tools/          — Interactive tools (character generator, etc.)
owlbear/        — Owlbear Rodeo VTT extension (future)
```

## Data Model

All game content lives as structured JSON in `data/`. Multiple frontends (website, character generator, VTT extension) read from the same data source.

| File | Contents |
|------|----------|
| `ancestries.json` | 7 ancestries with stat adjustments, traits, senses |
| `classes.json` | 10 classes with abilities, level charts, requirements |
| `attributes.json` | All 6 attribute modifier tables |
| `equipment.json` | Weapons, armor, shields, misc items, weapon groups |
| `spells.json` | Spell database (planned) |
| `monsters.json` | Bestiary (planned — from Monster Manual) |

## Status

**Phase 1 — In Progress**
- [x] Data model defined
- [x] Ancestries extracted (7/7)
- [x] Classes extracted (10/10)
- [x] Attribute tables extracted
- [x] Equipment extracted (armor, weapons, shields, misc items)
- [ ] Character generator
- [ ] Quartz site setup
- [ ] SRD content pages

## License

Game content is the intellectual property of Scott Myers. This repository contains the digital companion tools and SRD reference with permission.

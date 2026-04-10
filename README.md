# OpenClaw Skill Starter Pack: Minimal D&D 5e Character Creator

This pack is a minimal first version of a conversational skill that walks a user through creating a level 1 D&D 5e character and outputs JSON.

## Included files

- `skill-spec.yaml` — top-level skill contract
- `character-schema.json` — JSON schema for the character draft/output
- `session-state-schema.json` — JSON schema for conversation state
- `step-flow.json` — step order and branching rules
- `reference-data.json` — small starter dataset for classes, races, backgrounds, and spells
- `system-prompt.md` — behavior instructions for the agent

## Supported content

### Classes
- Fighter
- Rogue
- Wizard
- Cleric

### Races
- Human
- Elf
- Dwarf
- Halfling

### Backgrounds
- Acolyte
- Criminal
- Soldier
- Sage

### Scope limits
- Level 1 only
- Minimal spell lists
- No subclasses
- No feats
- No equipment builder yet
- No multiclassing
- No full PDF sheet export

## Suggested implementation pattern

1. Load `reference-data.json`
2. Initialize `session-state-schema.json`
3. Use `step-flow.json` to choose the next question
4. Apply updates to the object shaped by `character-schema.json`
5. Recompute derived fields after each answer
6. Validate and continue until complete

## Notes

This starter pack is intentionally small so you can prove the conversational flow before scaling up to the full ruleset.

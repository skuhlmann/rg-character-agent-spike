Here’s the complete `SKILL.md` exactly as it sits on disk:

````markdown
---
name: dnd5e_character_creator_minimal
description: Conversational workflow for building a minimal level-1 D&D 5e (2014) character and exporting JSON drafts for fighter, rogue, wizard, and cleric.
---

## Overview

- Walk the user through level-1 character creation with beginner-friendly language.
- Load the bundled data below to drive prompts, validation, and derived values.
- Maintain session state between turns and keep asking the next required question from the flow.
- Allow partial exports at any time; when complete, return the final JSON in a fenced code block.

## Behavior Rules

- Ask one focused question at a time unless the user supplies multiple details in one message.
- Parse compact answers and fill multiple fields when possible.
- Use beginner-friendly language by default.
- Only ask spellcasting questions for spellcasting classes.
- Recompute derived values after every answer.
- Validate choices against reference data.
- Support partial export at any time.
- When complete, output final JSON in a fenced code block.

## Completion Criteria

- identity.class.primary is set.
- identity.class.level is set to 1.
- identity.race.name is set.
- identity.background is set.
- All six ability scores are assigned.
- Required class skill picks are completed.

## Step Flow

```json
{
  "version": "0.1.0",
  "steps": [
    {
      "id": "start",
      "title": "Start",
      "goal": "Determine whether the user wants guidance or recommendations.",
      "prompts": [
        "Great — let’s build your D&D character step by step. Do you already know what class or vibe you want, or would you like a few suggestions?"
      ]
    },
    {
      "id": "basics",
      "title": "Basics",
      "requiredFields": [
        "character.identity.characterName",
        "character.identity.class.primary",
        "character.identity.class.level",
        "character.identity.race.name",
        "character.identity.background"
      ],
      "questionOrder": [
        "character.identity.class.primary",
        "character.identity.class.level",
        "character.identity.race.name",
        "character.identity.background",
        "character.identity.characterName",
        "character.identity.playerName",
        "character.identity.alignment"
      ]
    },
    {
      "id": "ability_scores",
      "title": "Ability Scores",
      "requiredFields": [
        "character.meta.abilityScoreMethod",
        "character.abilities.strength.score",
        "character.abilities.dexterity.score",
        "character.abilities.constitution.score",
        "character.abilities.intelligence.score",
        "character.abilities.wisdom.score",
        "character.abilities.charisma.score"
      ],
      "questionOrder": [
        "character.meta.abilityScoreMethod",
        "character.abilities.strength.score",
        "character.abilities.dexterity.score",
        "character.abilities.constitution.score",
        "character.abilities.intelligence.score",
        "character.abilities.wisdom.score",
        "character.abilities.charisma.score"
      ],
      "notes": [
        "Only support standard array or manual entry in v0.1.",
        "If standard array is chosen, ask the user to assign 15, 14, 13, 12, 10, 8."
      ]
    },
    {
      "id": "class_choices",
      "title": "Class Choices",
      "requiredFields": ["character.skills"],
      "notes": [
        "Apply class saving throw proficiencies automatically.",
        "Ask for required class skill picks.",
        "Enable spellcasting only for wizard and cleric."
      ]
    },
    {
      "id": "background",
      "title": "Background",
      "requiredFields": [],
      "notes": [
        "Apply fixed starter background proficiencies and feature text from reference data."
      ]
    },
    {
      "id": "spellcasting",
      "title": "Spellcasting",
      "condition": "character.spellcasting.enabled === true",
      "requiredFields": [],
      "notes": [
        "Only ask for starter spells for wizard and cleric.",
        "Keep spell list tiny in v0.1."
      ]
    },
    {
      "id": "review",
      "title": "Review",
      "requiredFields": [],
      "notes": [
        "Show summary.",
        "Offer final JSON.",
        "Mark meta.isComplete true when minimum required fields are present."
      ]
    }
  ]
}
```
````

## Reference Data

```json
{
  "version": "0.1.0",
  "abilities": [
    "strength",
    "dexterity",
    "constitution",
    "intelligence",
    "wisdom",
    "charisma"
  ],
  "skills": {
    "acrobatics": "dexterity",
    "animalHandling": "wisdom",
    "arcana": "intelligence",
    "athletics": "strength",
    "deception": "charisma",
    "history": "intelligence",
    "insight": "wisdom",
    "intimidation": "charisma",
    "investigation": "intelligence",
    "medicine": "wisdom",
    "nature": "intelligence",
    "perception": "wisdom",
    "performance": "charisma",
    "persuasion": "charisma",
    "religion": "intelligence",
    "sleightOfHand": "dexterity",
    "stealth": "dexterity",
    "survival": "wisdom"
  },
  "races": {
    "human": {
      "speed": 30,
      "languages": ["Common"],
      "abilityBonuses": {
        "strength": 1,
        "dexterity": 1,
        "constitution": 1,
        "intelligence": 1,
        "wisdom": 1,
        "charisma": 1
      },
      "traits": ["Extra Language (simplified omitted in v0.1)"]
    },
    "elf": {
      "speed": 30,
      "languages": ["Common", "Elvish"],
      "abilityBonuses": {
        "dexterity": 2
      },
      "traits": ["Darkvision", "Keen Senses", "Fey Ancestry", "Trance"]
    },
    "dwarf": {
      "speed": 25,
      "languages": ["Common", "Dwarvish"],
      "abilityBonuses": {
        "constitution": 2
      },
      "traits": ["Darkvision", "Dwarven Resilience", "Stonecunning"]
    },
    "halfling": {
      "speed": 25,
      "languages": ["Common", "Halfling"],
      "abilityBonuses": {
        "dexterity": 2
      },
      "traits": ["Lucky", "Brave", "Halfling Nimbleness"]
    }
  },
  "backgrounds": {
    "acolyte": {
      "skillProficiencies": ["insight", "religion"],
      "languages": ["Any", "Any"],
      "feature": "Shelter of the Faithful"
    },
    "criminal": {
      "skillProficiencies": ["deception", "stealth"],
      "toolProficiencies": ["Thieves' Tools"],
      "feature": "Criminal Contact"
    },
    "soldier": {
      "skillProficiencies": ["athletics", "intimidation"],
      "toolProficiencies": ["Gaming Set", "Vehicles (land)"],
      "feature": "Military Rank"
    },
    "sage": {
      "skillProficiencies": ["arcana", "history"],
      "languages": ["Any", "Any"],
      "feature": "Researcher"
    }
  },
  "classes": {
    "fighter": {
      "hitDie": 10,
      "savingThrows": ["strength", "constitution"],
      "skillChoices": {
        "choose": 2,
        "options": [
          "acrobatics",
          "animalHandling",
          "athletics",
          "history",
          "insight",
          "intimidation",
          "perception",
          "survival"
        ]
      },
      "armorProficiencies": ["All Armor", "Shields"],
      "weaponProficiencies": ["Simple Weapons", "Martial Weapons"],
      "features": ["Fighting Style (not selected in v0.1)", "Second Wind"],
      "spellcasting": {
        "enabled": false
      }
    },
    "rogue": {
      "hitDie": 8,
      "savingThrows": ["dexterity", "intelligence"],
      "skillChoices": {
        "choose": 4,
        "options": [
          "acrobatics",
          "athletics",
          "deception",
          "insight",
          "intimidation",
          "investigation",
          "perception",
          "performance",
          "persuasion",
          "sleightOfHand",
          "stealth"
        ]
      },
      "armorProficiencies": ["Light Armor"],
      "weaponProficiencies": [
        "Simple Weapons",
        "Hand Crossbows",
        "Longswords",
        "Rapiers",
        "Shortswords"
      ],
      "features": ["Sneak Attack", "Thieves' Cant"],
      "spellcasting": {
        "enabled": false
      }
    },
    "wizard": {
      "hitDie": 6,
      "savingThrows": ["intelligence", "wisdom"],
      "skillChoices": {
        "choose": 2,
        "options": [
          "arcana",
          "history",
          "insight",
          "investigation",
          "medicine",
          "religion"
        ]
      },
      "armorProficiencies": [],
      "weaponProficiencies": [
        "Daggers",
        "Darts",
        "Slings",
        "Quarterstaffs",
        "Light Crossbows"
      ],
      "features": ["Spellcasting", "Arcane Recovery"],
      "spellcasting": {
        "enabled": true,
        "ability": "intelligence",
        "cantripsKnown": 3,
        "spellsKnown": 6,
        "spellSlots": {
          "1": 2
        }
      }
    },
    "cleric": {
      "hitDie": 8,
      "savingThrows": ["wisdom", "charisma"],
      "skillChoices": {
        "choose": 2,
        "options": ["history", "insight", "medicine", "persuasion", "religion"]
      },
      "armorProficiencies": ["Light Armor", "Medium Armor", "Shields"],
      "weaponProficiencies": ["Simple Weapons"],
      "features": [
        "Spellcasting",
        "Divine Domain (simplified omitted in v0.1)"
      ],
      "spellcasting": {
        "enabled": true,
        "ability": "wisdom",
        "cantripsKnown": 3,
        "preparedFormula": "level + wisdom modifier",
        "spellSlots": {
          "1": 2
        }
      }
    }
  },
  "spells": {
    "wizard": {
      "cantrips": [
        "Fire Bolt",
        "Light",
        "Mage Hand",
        "Prestidigitation",
        "Ray of Frost"
      ],
      "level1": [
        "Magic Missile",
        "Shield",
        "Detect Magic",
        "Mage Armor",
        "Sleep",
        "Thunderwave"
      ]
    },
    "cleric": {
      "cantrips": [
        "Guidance",
        "Light",
        "Sacred Flame",
        "Spare the Dying",
        "Thaumaturgy"
      ],
      "level1": [
        "Bless",
        "Cure Wounds",
        "Detect Magic",
        "Guiding Bolt",
        "Healing Word",
        "Shield of Faith"
      ]
    }
  },
  "abilityScoreMethods": {
    "standard_array": [15, 14, 13, 12, 10, 8],
    "manual_entry": "freeform"
  }
}
```

## Character Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Minimal DND5E Character Draft",
  "type": "object",
  "required": [
    "schemaVersion",
    "game",
    "character"
  ],
  "properties": {
    "schemaVersion": {
      "type": "string",
      "const": "0.1.0"
    },
    "game": {
      "type": "object",
      "required": [
        "system",
        "edition"
      ],
      "properties": {
        "system": {
          "type": "string",
          "const": "DND5E"
        },
        "edition": {
          "type": "string",
          "const": "2014"
        }
      }
    },
    "character": {
      "type": "object",
      "required": [
        "identity",
        "abilities",
        "skills",
        "proficiency",
        "combat",
        "features",
        "spellcasting"
      ],
      "properties": {
        "identity
...(truncated)...
```

---
name: hytale-npc-modding
description: Use this skill for creating, defining behaviors (AI), stats, and models for NPCs in Hytale.
---

# Hytale NPC Modding Skill

## Core Mandates
- **Always read** `references/npc-modding-docs.md` before generating NPC JSON or AI behavior scripts.
- **Strictly enforce** the Hytale ECS (Entity Component System) architecture when defining NPC components.
- **Never use** hardcoded values for stats; always define them within the `stats` block or via specific components.
- **Always link** NPCs to a valid `modelId` and ensure `role` matches the intended AI behavior template.
- **Enforce** sensory-driven logic (Vision, Hearing, Smell) for all AI "Sensors" to maintain immersion.
- **Always validate** that behaviors include "Sensors", "Actions", and "Motions" in every instruction list.

## NPC Definition Standards
1. **Stats:** Must include `health`, `movementSpeed`, `viewDistance`, and `attackDamage` as base attributes.
2. **AI Behavior:** Organize logic into "Instruction Lists" using the modular Builder system.
3. **Models:** Assign geometry via `modelId` and define attachments (weapons/armor) in the `attachments` array.

## Input/Output Examples

### Example 1: Custom Merchant NPC
**User:** "Create a custom merchant NPC named 'Traveling Alchemist' that wanders the forest and trades potions."
**AI Behavior:**
- **Model:** `hytale:human_male_alchemist`
- **Role:** `hytale:passive_merchant`
- **Stats:** `health: 80`, `movementSpeed: 0.2`
- **Behavior:** Uses a `Wander` motion and a `TradeAction` sensor when approached by a player.

### Example 2: Aggressive Enemy AI
**User:** "Define an AI for a 'Shadow Stalker' that stays hidden until the player gets close, then lunges."
**AI Behavior:**
- **Sensor:** `Vision` (Range: 5) + `StealthComponent` (Active).
- **Action:** `PlayAnimation: "lunge_attack"` when `DetectedEntity` is within range.
- **Motion:** `Seek` at high speed (1.5) once triggered.

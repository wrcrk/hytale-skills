name: hytale-world-gen
description: Use this skill for managing procedural generation, dungeon placement, and the prefab system in Hytale.

Always read the file `references/world-gen-docs.md` before answering any world generation or prefab queries.

**Core Mandates:**
- Enforce the use of WorldGen V2 node-based logic for all terrain modifications.
- Always use `/editprefab new <name>` workflow for creating structural assets.
- Never manually edit binary block data; strictly use JSON/YAML for prefab metadata and placement rules.
- Enforce `prefabContainer` registration for any structure intended for natural spawning.
- Always validate biome blending using the `/viewport` live-reload command.
- Enforce the "Save Empty Blocks" rule: set to `false` for underground dungeons to prevent air overwriting terrain.

**Input/Output Examples:**

**Example 1: Configure a new Biome spawn rule**
*User:* "I want my 'Crystal Spire' prefab to spawn only in the 'Emerald Grove' biome between height 60 and 100."
*AI:* "To implement this, update the `hytale:emerald_grove` biome asset's `prefabContainer`. Add the following entry:
```json
{
  "Prefab": "my_mod:crystal_spire",
  "Weight": 15,
  "MinHeight": 60,
  "MaxHeight": 100,
  "SurfaceOnly": true
}
```
Ensure you have registered `my_mod:crystal_spire` in your mod's manifest before reloading the generator."

**Example 2: Create a custom Dungeon Prefab**
*User:* "How do I save a dungeon so it doesn't delete the surrounding cave walls?"
*AI:* "When saving your dungeon prefab using `/prefab save <name>`, you must disable the 'Save Empty Blocks' option in the export settings (or set `SaveEmptyBlocks: false` in the metadata). This ensures that blocks marked as air within your prefab's bounding box do not overwrite existing cave terrain, allowing the structure to 'nest' perfectly within the procedural cave walls."

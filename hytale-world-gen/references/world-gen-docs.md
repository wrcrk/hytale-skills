# Hytale World Generation & Prefab Technical Documentation

## 1. World Generation Hierarchy (V2 Engine)
Hytale's world generation is a multi-layered procedural system managed via a node-based architecture.

- **Zones:** Large-scale regional containers (e.g., Zone 1: Emerald Grove). They define the high-level noise maps and available biome sets.
- **Biomes:** Specific terrain and environmental units. Biomes in V2 *dictate* terrain shape via density functions, rather than just skinning existing terrain.
- **Caves:** Independent procedural networks generated within Zones, often utilizing different noise algorithms (e.g., Worley/Cellular noise) than surface terrain.

## 2. Biome Configuration (`biome.json`)
Biomes are modular assets defined by five primary logical structures:

### A. Terrain Shape (HeightmapInterpreter)
Uses mathematical noise nodes to calculate physical geometry.
- **Nodes:** Constant, Noise (Perlin/Simplex), Math (Add/Multiply), Select (for blending).
- **Logic Flow:** Nodes are evaluated right-to-left in the Hytale Asset Node Editor.

### B. Material Providers
Determines block composition based on depth and slope.
- **CoverContainer:** Defines the surface layer (e.g., `hytale:grass_block`).
- **LayerContainer:** Defines subsurface strata (e.g., 3 blocks of `hytale:dirt`, then `hytale:stone`).

### C. Prop Generation (PrefabContainer)
Controls the placement of static objects and structures.
- **Weight:** Relative frequency of spawning.
- **Heuristics:** Conditional logic (e.g., "Spawn only if slope < 15 degrees" or "Spawn only near water").

### D. Environment & Tints
- **EnvironmentProvider:** Links to `.env` files for weather, fog, and skybox.
- **ColorProvider:** Maps hex codes to block tints for grass and foliage.

## 3. Prefab System (Intelligent Blueprints)
Prefabs are reusable structures saved as JSON metadata with associated block data.

### Technical Metadata Structure
```json
{
  "Id": "mod_id:structure_name",
  "Size": [width, height, depth],
  "Origin": [x, y, z],
  "SaveEmptyBlocks": false,
  "PlacementRules": {
    "AllowedBiomes": ["hytale:emerald_grove"],
    "SurfaceOnly": true,
    "TerrainRestrictions": ["hytale:dirt", "hytale:grass_block"]
  },
  "Entities": [
    { "Id": "hytale:npc_skeleton_guard", "Pos": [5, 1, 5], "Rotation": 90 }
  ]
}
```

### Prefab Workflow
1. **Initiation:** `/editprefab new <name>` creates a clean-room world instance.
2. **Construction:** Build using standard blocks or the **Asset Editor** for custom entities.
3. **Selection:** Use the **Selection Brush (Hotkey: K)** to define the bounding box.
4. **Saving:** `/prefab save <name>` generates the `.json` and block data.
5. **Registration:** Add the Prefab ID to the `prefabContainer` of the target biome.

## 4. Technical Tools & Commands
- **Asset Node Editor:** (In-game: `B` > Asset Editor) Visual tool for `biome.json` logic.
- **`/viewport --radius <n>`:** Creates a live-update zone. Changes to world-gen files will trigger an immediate re-generation within this radius.
- **`/instances spawn <instance_id>`:** Generates a fresh world using a specific generator configuration for stress testing.
- **`Instance.bson`:** The root configuration for a world instance, defining which `HytaleGenerator` and Zone/Biome sets to initialize.

## 5. Structural Rules & Constraints
- **Collision:** The generator automatically checks for prefab-to-prefab collision. High-weight prefabs should have high `Spacing` values to prevent clustering.
- **Empty Blocks:** For underground structures, always set `SaveEmptyBlocks` to `false`. This allows procedural terrain (like ore veins) to remain inside the structure's "air" pockets.
- **Palette Mapping:** Prefabs use a palette system to map local character IDs to global Block IDs, optimizing file size.

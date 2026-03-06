# Hytale NPC Modding Documentation

## 1. Core Architecture: Entity Component System (ECS)
Hytale uses a highly modular ECS (specifically using the **Flecs** library) for all entities. NPCs are compositions of data-only **Components** and logic-heavy **Systems**.

### 1.1 NPC Definition Structure
NPCs are defined in JSON files, typically located in `data/entities/npc/`.

```json
{
  "id": "hytale:custom_npc_id",
  "model": {
    "modelId": "hytale:model_path",
    "attachments": [
      { "slot": "hand_right", "itemId": "hytale:iron_sword" }
    ]
  },
  "role": "hytale:behavior_role_id",
  "stats": {
    "health": 100,
    "movementSpeed": 0.25,
    "viewDistance": 24.0,
    "attackDamage": 10
  }
}
```

## 2. AI & Behavior System
Behaviors are defined through **Roles** which contain modular **Instruction Lists**.

### 2.1 Behavioral Builders
- **Sensors:** Perceive internal or external states.
    - `Vision`: Checks view cones/sectors for entities.
    - `Hearing`: Detects footsteps or block sounds.
    - `Chemical/Odor`: Used by animals for tracking.
    - `IsBusy`: Checks if the NPC is currently performing an animation/task.
- **Actions:** Discrete logical triggers.
    - `SetTarget`: Assigns a `Blackboard` target.
    - `StartObjective`: Triggers a high-level scripted goal.
    - `PlayAnimation`: Executes a visual state.
    - `Beacon`: Signals nearby allies.
- **Motions:** Movement logic.
    - `Seek`: Move toward a target.
    - `Flee`: Move away from a target.
    - `Wander`: Random movement within a radius.

### 2.2 Blackboard Memory
NPCs store transient data (targets, current state, local variables) in a **Blackboard**. 
- **Target Memory:** Stores `hostile_target_memory` for combat persistence.
- **Variables:** Can be accessed by sensors to decide weights in a **Utility System**.

## 3. NPC Stats & Attributes
Stats are managed through components that can be dynamically modified.
- **Base Stats:** Defined in the entity JSON.
- **Stat Actions:** `SetStat` and `AddStat` are used in behavior scripts to adjust values (e.g., gaining speed when "Enraged").
- **Utility Evaluator:** A mathematical system that weights actions based on environment (light, weather) and internal state (hunger, health).

## 4. Models and Visuals
Models are block-based JSON files created in **Hytale Model Maker**.

### 4.1 Model Assignment
```json
"model": {
  "modelId": "hytale:trork_warrior",
  "textureVariant": "desert",
  "scaling": 1.1
}
```

### 4.2 Attachments & Bones
Models define **Bones** (pivots) where items or effects can be attached.
- **Standard Slots:** `head`, `hand_right`, `hand_left`, `back`, `feet`.
- **Dynamic Scaling:** Bones can be scaled individually via behavior scripts (e.g., a "Giant" variant of a standard NPC).

## 5. Debugging Commands
Use these commands in-game to test NPC logic:
- `/npc role <role>`: Swap behavior roles on the fly.
- `/npc step`: Advance the AI clock by one frame.
- `/npc blackboard`: View current targets and stored variables.
- `/npc component`: Inspect raw ECS component data.
- `/spawning stats`: Monitor AI performance and density.

## 6. Factions & Relationships
NPCs belong to **Factions** which determine their initial `Targeting` sensor logic.
- **Factions:** `hytale:outlanders`, `hytale:kweebecs`, `hytale:trorks`, `hytale:scaracks`.
- **Relationship Matrix:** Defined globally but can be overridden per-NPC instance.

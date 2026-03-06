# Hytale Modding - Entity Component System (ECS) & Entities

This document details the core architecture and implementation rules for Hytale's Entity Component System (ECS) as of the current modding API.

## 1. Fundamental ECS Concepts
Hytale uses a pure ECS architecture where data is separated from logic.

*   **Entities (`Ref<EntityStore>`)**: Unique, long-lived IDs that act as containers for components. They contain no logic or data themselves.
*   **Components (`Component<EntityStore>`)**: Data-only structures. They must be `Cloneable` and typically have a `Codec` for JSON serialization.
*   **Systems (`EntityTickingSystem`)**: Logic processors that filter entities by their components and perform operations during the world tick.
*   **Store (`EntityStore`)**: The repository for all entities and components within a specific world/dimension.

## 2. Entity JSON Definitions (Templates)
Entities are defined via JSON assets, typically located in `assets/server/entities/`.

### Structure Example:
```json
{
  "Id": "mod_id:vanguard_soldier",
  "Archetype": "hytale:living_entity",
  "Components": {
    "hytale:transform": {
      "Position": { "x": 100, "y": 64, "z": -250 },
      "Rotation": { "x": 0, "y": 180, "z": 0 }
    },
    "hytale:health": {
      "MaxHealth": 200,
      "CurrentHealth": 200
    },
    "hytale:alignment": {
      "Faction": "mod_id:defenders"
    },
    "mod_id:mana": {
      "MaxMana": 50,
      "RegenRate": 1.5
    }
  }
}
```

### Key JSON Fields:
*   **Id**: Namespace-qualified unique identifier.
*   **Archetype**: Inherits components from a base template (e.g., `hytale:living_entity`, `hytale:projectile`).
*   **Components**: A map of Component ID to its initial data values.

## 3. Component Implementation (Java)
Components must hold data only. Avoid logic inside these classes.

```java
public class ManaComponent implements Component<EntityStore> {
    public float currentMana;
    public float maxMana;
    public float regenRate;

    @Override
    public ManaComponent clone() {
        ManaComponent clone = new ManaComponent();
        clone.currentMana = this.currentMana;
        clone.maxMana = this.maxMana;
        clone.regenRate = this.regenRate;
        return clone;
    }
}
```

### Component Registration
Register components in your plugin's `setup()` method:
```java
ComponentType<EntityStore, ManaComponent> MANA_TYPE = registry.registerComponent(
    "mod_id:mana", 
    ManaComponent.class, 
    new ManaComponentCodec()
);
```

## 4. System Implementation (Logic)
Systems iterate over entities using a `Query`.

### Example System:
```java
public class ManaRegenSystem extends EntityTickingSystem<EntityStore> {
    private final ComponentType<EntityStore, ManaComponent> manaType;

    public ManaRegenSystem(ComponentType<EntityStore, ManaComponent> manaType) {
        this.manaType = manaType;
    }

    @Override
    public Query<EntityStore> getQuery() {
        // Only process entities that have the ManaComponent
        return Query.all(manaType).build();
    }

    @Override
    public void tick(float dt, int index, ArchetypeChunk<EntityStore> chunk, Store<EntityStore> store, CommandBuffer<EntityStore> buffer) {
        ManaComponent mana = chunk.getComponent(index, manaType);
        
        if (mana.currentMana < mana.maxMana) {
            mana.currentMana = Math.min(mana.maxMana, mana.currentMana + (mana.regenRate * dt));
        }
    }
}
```

## 5. Critical Modding Rules & Limits
*   **CommandBuffer**: ALWAYS use the `CommandBuffer` for adding, removing, or destroying entities/components during a `tick`. Direct modification causes race conditions.
*   **Data vs. Logic**: Never put game logic inside a Component. Logic belongs in Systems.
*   **Archetypes**: Entities with the same set of components share an Archetype. Excessive unique component combinations can impact performance.
*   **Query Filtering**:
    *   `Query.all(C1, C2)`: Entity MUST have both.
    *   `Query.any(C1, C2)`: Entity must have at least one.
    *   `Query.none(C3)`: Entity must NOT have C3.

## 6. Common Standard Components
| ID | Description |
| :--- | :--- |
| `hytale:transform` | Location, rotation, and scale. |
| `hytale:health` | HP management and death state. |
| `hytale:physics` | Collision and velocity. |
| `hytale:rendering` | Model and texture configuration. |
| `hytale:ai` | Behavioral state and pathfinding. |
| `hytale:inventory` | Item storage and equipment. |

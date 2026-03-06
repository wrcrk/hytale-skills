---
name: hytale-ecs-core
description: Use this skill for designing and managing Entities and Components using Hytale's Entity Component System (ECS).
---

# Hytale ECS Core Skill

You are an expert in Hytale's Entity Component System (ECS). You must design entities, components, and systems according to the strict architectural separation of data and logic.

## Core Directives
1. **Always Read Documentation:** Before providing any ECS code or JSON, read `references/ecs-core-docs.md` to ensure compliance with the latest API rules and entity structures.
2. **Enforce Data/Logic Separation:** Never include logic methods within Component classes. All logic must reside within Systems or specialized Utility classes.
3. **Use CommandBuffer for Mutations:** Always use the `CommandBuffer` when adding or removing components or entities inside a system's `tick` method.
4. **Implement Cloneable Components:** All custom Java components must implement the `Component` interface and provide a deep `clone()` method.
5. **Standardize Naming:** Follow Hytale's `namespace:id` convention for all component and entity identifiers.

## Examples

### Example 1: Creating a Custom Component
**User:** "I need a component to track an entity's 'Oxygen' level for underwater breathing."
**AI:**
```java
public class OxygenComponent implements Component<EntityStore> {
    public float currentOxygen;
    public float maxOxygen;

    @Override
    public OxygenComponent clone() {
        OxygenComponent clone = new OxygenComponent();
        clone.currentOxygen = this.currentOxygen;
        clone.maxOxygen = this.maxOxygen;
        return clone;
    }
}
```

### Example 2: Designing an Entity JSON
**User:** "Create a JSON for a 'Fire Elemental' that uses the living entity archetype and has a custom 'Heat' component."
**AI:**
```json
{
  "Id": "my_mod:fire_elemental",
  "Archetype": "hytale:living_entity",
  "Components": {
    "hytale:rendering": {
      "Model": "my_mod:models/entities/fire_elemental.json"
    },
    "my_mod:heat": {
      "Temperature": 500.0,
      "AuraRadius": 5.0
    }
  }
}
```

---
name: hytale-interactions-media
description: Use this skill for managing player interactions with blocks/entities, integrating sound effects, and configuring Argument Types (ArgTypes) in Hytale.
---

# Hytale Interactions, Sounds, and ArgTypes

Always read `references/interactions-media-docs.md` before implementing or advising on interactions and media.

## Core Mandates
- **Always enforce** correct `ArgType` usage in JSON and Java to prevent engine parsing errors.
- **Never use** hardcoded sound IDs; always retrieve them from the `AssetMap` using `SoundEvent.getAssetMap().getIndex()`.
- **Strictly apply** `SimpleInstantInteraction` for one-shot behaviors and `SimpleInteraction` for ticked logic.
- **Enforce mono-channel** sounds for `WorldSoundEventId` to ensure proper 3D positioning.
- **Always link** interactions in `my_item.json` under the `Interactions` key (Primary/Secondary).
- **Verify** `TransformComponent` presence before triggering 3D sounds in Java.
- **Utilize Beacons** for cross-NPC communication instead of direct entity references.

## Examples

### Example 1: Triggering a Custom Sound in Java
**User:** How do I play a specific sound when a player uses an item?
**AI:** First, obtain the sound index from the `AssetMap`. Then, execute the play command within the world context.
```java
int index = SoundEvent.getAssetMap().getIndex("SFX_Cactus_Large_Hit");
world.execute(() -> {
    TransformComponent transform = store.getComponent(playerRef, EntityModule.get().getTransformComponentType());
    SoundUtil.playSoundEvent3dToPlayer(playerRef, index, SoundCategory.SFX, transform.getPosition(), store);
});
```

### Example 2: Configuring a Charging Interaction in JSON
**User:** Create a food item that takes 2 seconds to eat.
**AI:** Use a `Charging` interaction type in the item's JSON definition.
```json
"Secondary": {
  "Interactions": [
    {
      "Type": "Charging",
      "FailsOnDamage": true,
      "HorizontalSpeedMultiplier": 0.4,
      "Next": {
        "2.0": { "Type": "my_custom_eat_behavior" }
      }
    }
  ]
}
```

# Hytale Interactions, Sounds, and ArgTypes - Technical Documentation

## 1. Argument Types (ArgTypes)
ArgTypes are used in command argument definitions and JSON asset parsing to specify the expected data type.

| ArgType Name | Java Target Class | Description |
| :--- | :--- | :--- |
| **Boolean** | `java.lang.Boolean` | 'true' or 'false' input. |
| **Integer** | `java.lang.Integer` | Whole number. |
| **String** | `java.lang.String` | Text/numbers (use quotes for multi-word). |
| **Float** | `java.lang.Float` | Floating-point number. |
| **Double** | `java.lang.Double` | Decimal number. |
| **UUID** | `java.util.UUID` | Universally Unique IDentifier. |
| **Player_UUID** | `java.util.UUID` | UUID or online player username. |
| **Relative_Double_Coord** | `Coord` | Decimal coord; use `~` for relative position. |
| **Player_Ref** | `PlayerRef` | UUID or online player username. |
| **World** | `World` | World folder name. |
| **Interaction_Asset** | `Interaction` | Reference to an Interaction asset. |
| **Sound_Event_Asset** | `SoundEvent` | Reference to a SoundEvent asset. |
| **Sound_Category** | `SoundCategory` | Enum (sfx, music, ambient, ui). |
| **Item_Asset** | `Item` | Reference to an Item asset. |
| **Block_Type_Asset** | `BlockType` | Reference to a BlockType asset. |

---

## 2. Interactions Framework

### Base Interaction Structure (JSON)
Every interaction inherited from `Interaction` supports these fields:

| Field Name | Type | Description |
| :--- | :--- | :--- |
| **ViewDistance** | `Double` (96.0) | Max distance players can see effects. |
| **Effects** | `InteractionEffects` | Sounds, animations, and particles. |
| **HorizontalSpeedMultiplier** | `Float` (1.0) | Movement speed multiplier during execution. |
| **RunTime** | `Float` | Minimum duration of the interaction. |
| **CancelOnItemChange** | `Boolean` (true) | Cancel if player switches items. |
| **Rules** | `InteractionRules` | Cancellation and blocking logic. |

### Interaction Effects (`InteractionEffects`)
- **WorldSoundEventId**: Mono sound played at entity location.
- **LocalSoundEventId**: 2D sound played for the user (stereo).
- **Particles / FirstPersonParticles**: Visual effects for different camera modes.
- **ItemAnimationId**: String ID of the animation to trigger.
- **ClearSoundEventOnFinish**: Stop the sound when interaction ends.

### Common Interaction Types (JSON)
- **Condition**: Logic gate checking state (e.g., `Crouching: true`).
- **Charging**: Requires holding the interaction for `X` seconds.
- **Serial**: Executes a list of interactions in sequence.
- **DamageEntity**: Logic for damaging targets with angled/targeted offsets.
- **Projectile**: Spawns a projectile from `ProjectileConfig`.
- **ChangeStat**: Modifies player stats (e.g., `Health`, `Stamina`).

---

## 3. Java Implementation (Server Plugins)

### Custom Interaction Logic
Inherit from `SimpleInstantInteraction` for immediate actions or `SimpleInteraction` for logic that needs to `tick()`.

```java
public class MyCustomInteraction extends SimpleInstantInteraction {
    public static final BuilderCodec<MyCustomInteraction> CODEC = BuilderCodec.builder(
            MyCustomInteraction.class, MyCustomInteraction::new, SimpleInstantInteraction.CODEC
    ).build();

    @Override
    protected void firstRun(@Nonnull InteractionType interactionType, 
                            @Nonnull InteractionContext interactionContext, 
                            @Nonnull CooldownHandler cooldownHandler) {
        // Implementation
        Player player = interactionContext.getCommandBuffer().getComponent(
            interactionContext.getEntity(), Player.getComponentType());
        player.sendMessage(Message.raw("Interaction Triggered!"));
    }
}
```

### Registration
Register the interaction in your plugin's `setup()` method:
```java
this.getCodecRegistry(Interaction.CODEC)
    .register("my_interaction_id", MyCustomInteraction.class, MyCustomInteraction.CODEC);
```

---

## 4. Sound Implementation

### Playing Sounds in Java
Sounds must be played through the `SoundUtil` class. 

**Requirements:**
1. `PlayerRef`: Reference to the player.
2. `SoundIndex`: Integer ID from `SoundEvent.getAssetMap()`.
3. `SoundCategory`: Enum (Music, Ambient, SFX, UI).
4. `TransformComponent`: Positioning data.

```java
int index = SoundEvent.getAssetMap().getIndex("SFX_Cactus_Large_Hit");
world.execute(() -> {
    TransformComponent transform = store.getComponent(playerRef, 
        EntityModule.get().getTransformComponentType());
    SoundUtil.playSoundEvent3dToPlayer(playerRef, index, SoundCategory.SFX, 
        transform.getPosition(), store);
});
```

### Critical Rules for Sounds
- **Mono vs Stereo**: `WorldSoundEventId` MUST be mono for 3D spatialization.
- **Category Enforcement**: UI sounds should use `SoundCategory.UI` to bypass world-space attenuation.

---

## 5. NPC Interactions & Beacons
NPCs communicate using **Beacons** rather than direct pointers to other entities.

### Beacon Sensor (JSON)
```json
{
  "Sensor": {
    "Type": "Beacon",
    "Message": "Annoy_Ogre",
    "Range": 5
  },
  "Actions": [
    {
      "Type": "Attack",
      "Attack": { "Compute": "SleepingAttack" }
    }
  ]
}
```
- **Triggering Beacons**: NPCs use `TriggerSpawnBeacon` or broadcast messages to nearby entities.
- **NPC Groups**: Define `IncludeRoles` in an NPC group to filter which entities react to specific interactions.

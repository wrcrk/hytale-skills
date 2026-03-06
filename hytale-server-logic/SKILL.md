---
name: hytale-server-logic
description: Use this skill for managing server-side logic, developing server plugins, handling gameplay systems, and intercepting game events in Hytale.
---

# Hytale Server Logic Skill

## Mandatory Procedures
1. **Always read** `references/server-logic-docs.md` before generating any plugin code or explaining gameplay systems.
2. **Enforce Java 25** standards for all code snippets. Hytale server-side development requires Java 25.
3. **Distinguish between Standard and ECS Events:**
   - Use `getEventBus()` for lifecycle and player management (e.g., `AddPlayerToWorldEvent`).
   - Use `EntityEventSystem<T>` for world and entity interactions (e.g., `BreakBlockEvent`, `DamageEvent`).
4. **Enforce Entity Component System (ECS) Principles:** Never attempt to modify entities directly; always use a `CommandBuffer` and retrieve components from the `EntityStore`.
5. **Never use client-side libraries:** All logic must be "Server-First" to ensure compatibility with Hytale's authoritative server model.

## Examples

### Example 1: Welcoming Players (Standard Event)
**User:** "Write a plugin that sends a welcome message when a player joins."
**AI Behavior:** 
```java
public class WelcomePlugin extends JavaPlugin {
    @Override
    public void setup() {
        this.getEventBus().register(AddPlayerToWorldEvent.class, event -> {
            Player player = event.getPlayer();
            player.sendMessage(Message.raw("Welcome to the server, " + player.getName() + "!"));
        });
    }
}
```

### Example 2: Block Break Notification (ECS Event)
**User:** "How do I intercept a block break event to notify the player?"
**AI Behavior:** Implement an `EntityEventSystem` for `BreakBlockEvent` and retrieve the `Player` component from the entity reference.

```java
public class BlockBreakSystem extends EntityEventSystem<BreakBlockEvent> {
    public BlockBreakSystem() { super(BreakBlockEvent.class); }

    @Override
    public void handle(EntityStore store, BreakBlockEvent event, ArchetypeChunk chunk, int index, CommandBuffer buffer) {
        EntityStore.Reference entityRef = chunk.getReference(index);
        Player player = store.getComponent(entityRef, Player.class);
        if (player != null) {
            player.sendMessage(Message.raw("You mined: " + event.getBlockType().getId()));
        }
    }
}
```

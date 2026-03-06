# Hytale Server Logic & Plugin Development Reference

## Core Architecture: Server-First & ECS
Hytale operates on a **Server-First** authority model. All gameplay logic, physics, and interactions are processed on the server. The architecture is built upon an **Entity Component System (ECS)**.

### Technical Requirements
- **Language:** Java 25
- **Build Tool:** Gradle (standard for plugin templates)
- **Architecture:** ECS (Entity Component System)
- **Data Format:** JSON (for world gen and item definitions)

---

## 1. Plugin Structure
Every server plugin must extend `JavaPlugin`. The `setup()` method is the entry point for registering event listeners and ECS systems.

```java
import common.plugin.JavaPlugin;

public class MyPlugin extends JavaPlugin {
    @Override
    public void setup() {
        // Registration logic goes here
    }
}
```

---

## 2. Event Handling Systems

### A. Standard Events (EventBus)
Used for high-level lifecycle events and player management. These are handled via the `EventBus`.

| Event Name | Description |
| :--- | :--- |
| `AddPlayerToWorldEvent` | Triggered when a player successfully joins the world. |
| `PlayerChatEvent` | Intercepts player chat messages. |
| `PlayerReadyEvent` | Triggered when the player's client is fully loaded. |

**Example Registration:**
```java
this.getEventBus().register(PlayerChatEvent.class, event -> {
    // Logic here
});
```

### B. ECS Events (EntityEventSystem)
Used for performance-critical world interactions. These events are processed in "Systems" that iterate over entity chunks.

| Event Name | Description |
| :--- | :--- |
| `BreakBlockEvent` | Triggered when an entity breaks a block. |
| `PlaceBlockEvent` | Triggered when an entity places a block. |
| `DamageEvent` | Triggered when an entity takes damage. |

**Example Implementation:**
```java
public class BlockBreakSystem extends EntityEventSystem<BreakBlockEvent> {
    public BlockBreakSystem() {
        super(BreakBlockEvent.class);
    }

    @Override
    public void handle(EntityStore store, BreakBlockEvent event, ArchetypeChunk chunk, int index, CommandBuffer buffer) {
        // handle logic
    }
}
```

---

## 3. ECS Concepts & API

### EntityStore
The central registry for all entities and components. 
- `store.getComponent(entityRef, ComponentClass.class)`: Retrieves a specific component from an entity.
- `store.hasComponent(entityRef, ComponentClass.class)`: Checks for existence.

### CommandBuffer
Used to queue modifications to the world state. **Never modify the state directly inside a system's handle method; use the buffer.**
- `buffer.addComponent(entityRef, new MyComponent())`
- `buffer.removeComponent(entityRef, MyComponent.class)`

### ArchetypeChunk
A collection of entities that share the same set of components. Processing happens in chunks to optimize CPU cache usage.

---

## 4. Server Setup & Testing
1. **JDK:** Ensure `JAVA_HOME` points to a Java 25 installation.
2. **Auth Login:** Use `auth login device` in the server console for initial setup.
3. **Persistence:** Set `auth persistence Encrypted` to avoid re-authenticating every restart.
4. **Local Testing:** Plugins are loaded from the `/plugins` directory. Use the `HytaleServer` run configuration in your IDE.

---

## 5. API Limits & Best Practices
- **Strict Authority:** The server has the final say. Do not trust client-sent data (if any).
- **Asynchronous Operations:** Use the Hytale task scheduler for IO-heavy tasks (database, web requests) to avoid blocking the main tick loop.
- **Component Design:** Keep components small and data-only. Put all logic in Systems.

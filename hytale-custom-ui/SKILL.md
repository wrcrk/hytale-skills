---
name: hytale-custom-ui
description: Use this skill for designing, formatting, and scripting custom UI windows, menus, and HUDs for players in Hytale.
---

# Hytale Custom UI Skill

Expertise in Hytale's declarative `.ui` markup and Java-based server-side control logic.

### Critical Mandates
- **Read First:** Always read `references/custom-ui-docs.md` before generating any UI markup or Java code.
- **Enforce Structure:** Place all `.ui` files in `resources/Common/UI/Custom`.
- **Strict Syntax:** Always use the `Group { Element #ID { ... } }` structure for markup.
- **Anchor Logic:** Enforce `Anchor: (Top: X, Left: Y, Width: W, Height: H);` for precise positioning.
- **Asset Integrity:** Remind modders that `manifest.json` MUST contain `"IncludesAssetPack": true`.
- **Performance:** Never send redundant `UICommandBuilder` updates; batch changes whenever possible.

### Input/Output Examples

**User Request:** "Create a simple custom health bar HUD."
**Response:** 
1. Create `resources/Common/UI/Custom/HealthBar.ui`:
```hytale-ui
Group {
    ProgressBar #PlayerHealth {
        Style: $Common.@DefaultProgressBarStyle;
        Background: #330000;
        Foreground: #FF0000;
        Anchor: (Bottom: 50, Horizontal: 200, Height: 20);
    }
}
```
2. Java Controller:
```java
public void updateHealth(UICommandBuilder builder, float percent) {
    builder.set("#PlayerHealth", "Value", percent);
}
```

**User Request:** "Create a shop menu with a 'Close' button."
**Response:**
```hytale-ui
Group #ShopMenu {
    Background: $Common.@MenuBackground;
    Anchor: (Vertical: 400, Horizontal: 600);
    Button #CloseBtn {
        Text: "Exit Shop";
        Style: $Common.@PrimaryButtonStyle;
        Anchor: (Top: 10, Right: 10, Width: 100, Height: 40);
    }
}
```
Binding the action in Java:
```java
builder.onEvent("#CloseBtn", "Click", (player) -> {
    builder.remove("#ShopMenu");
});
```

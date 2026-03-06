# Hytale Custom UI Documentation Reference

## 1. Core Architecture
Hytale uses a declarative markup system (`.ui` files) to define interfaces. These are managed server-side via the Java `UICommandBuilder` API, which synchronized state to the client.

### File Locations
- **Markup Files:** `resources/Common/UI/Custom/*.ui`
- **Styles/Variables:** `resources/Common/UI/Variables/*.json`
- **Manifest Requirement:** 
  ```json
  {
      "IncludesAssetPack": true
  }
  ```

## 2. UI Markup Syntax (.ui)
Markup uses a hierarchical structure. Elements are defined within `Group` containers. Use `#` to define an ID for targeting via Java.

### Basic Structure
```hytale-ui
Group #ContainerName {
    Label #Title {
        Text: "My Menu";
        Style: $Common.@TitleStyle;
        Anchor: (Top: 20, Horizontal: 0);
    }
}
```

### Common Elements
| Element | Description | Key Properties |
| :--- | :--- | :--- |
| `Group` | Container for other elements | `Layout`, `ClipChildren`, `Background` |
| `Label` | Text display | `Text`, `Color`, `FontSize`, `TextAlignment` |
| `Button` | Interactive button | `Text`, `Enabled`, `SoundOnHover` |
| `Image` | Static or dynamic texture | `Texture`, `Color` |
| `ProgressBar` | Visual progress indicator | `Value` (0.0 to 1.0), `Foreground`, `Background` |
| `TextField` | User text input | `Placeholder`, `MaxLength`, `IsPassword` |

## 3. The Anchor System
The `Anchor` property defines positioning and scaling relative to the parent container.

| Field | Meaning |
| :--- | :--- |
| `Top`, `Bottom`, `Left`, `Right` | Offset from the respective edge. |
| `Width`, `Height` | Fixed dimensions in pixels. |
| `Horizontal`, `Vertical` | Centering/Stretching (e.g., `Horizontal: 0` centers). |
| `Full: 1` | Stretch to fill entire parent container. |

**Example: Stretching Bottom-Aligned Bar**
```hytale-ui
Anchor: (Bottom: 10, Left: 50, Right: 50, Height: 40);
```

## 4. Styling and Variables
Styles are referenced using the `$` (Namespace) and `@` (Variable) syntax.
- **Global Reference:** `Style: $Common.@DefaultLabelStyle;`
- **Inline Overrides:** `Style: (FontSize: 24, Color: #FFFFFF);`

## 5. Java API (UICommandBuilder)
The `UICommandBuilder` is the primary interface for manipulating the UI at runtime.

### Essential Methods
- `append(String uiFilePath)`: Loads and displays a `.ui` file.
- `set(String selector, String property, Object value)`: Updates a property of an element.
- `remove(String selector)`: Deletes an element from the UI.
- `clear()`: Wipes all custom UI elements from the screen.
- `onEvent(String selector, String eventType, Consumer<Player> action)`: Binds a server-side callback to a client event.

### Event Types
- `Click`: Triggered when an element is clicked.
- `Changed`: Triggered when a `TextField` value changes.
- `HoverEnter` / `HoverExit`: Triggered on mouse-over.

## 6. Debugging
Enable **Diagnostic Mode** in the Hytale Client settings (General -> Developer Tools) to see syntax errors, missing asset warnings, or selector mismatch errors.

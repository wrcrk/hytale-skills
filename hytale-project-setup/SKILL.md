---
name: hytale-project-setup
description: Use this skill for generating Hytale mod folder structures, initializing configurations, and setting up basic Java architecture.
---

# Hytale Project Setup Skill

Always read `references/project-setup-docs.md` before generating any project files or configurations. This skill enforces strict adherence to Hytale's Java plugin standards and folder hierarchies.

## Core Directives
- **Enforce Structure:** Always use the standard Gradle layout: `src/main/java` for code and `src/main/resources` for metadata/assets.
- **Java Standards:** Always use Java 25 (OpenJDK) for plugin development.
- **Metadata Requirement:** Every project must include a `manifest.json` in `src/main/resources`.
- **Inheritance Rule:** The entry point class must extend `JavaPlugin` from the Hytale API.
- **Build System:** Always default to Gradle with Kotlin DSL (`build.gradle.kts`).
- **Never Use:** Legacy `mod.json` naming; strictly use `manifest.json`.

## Examples

### Example 1: Initializing a New Plugin
**User:** "Initialize a Hytale plugin called 'DragonCore' for user 'Ignis'."
**Agent:** 
"Creating Hytale project structure for DragonCore:
```text
DragonCore/
├── src/main/java/com/ignis/dragoncore/DragonCore.java
├── src/main/resources/manifest.json
├── build.gradle.kts
└── settings.gradle.kts
```
(Agent then provides the content for `DragonCore.java` extending `JavaPlugin` and `manifest.json` with ID `com.ignis.dragoncore`.)"

### Example 2: Validating Metadata
**User:** "Check my manifest.json for errors."
**Agent:**
"Validating `manifest.json`...
- ERROR: Missing `Main` field pointing to the entry point.
- ERROR: `Id` must be lowercase and dot-separated.
Fixed version:
```json
{
  "Id": "com.user.mod",
  "Name": "My Mod",
  "Version": "1.0.0",
  "Main": "com.user.mod.MainClass"
}
```"

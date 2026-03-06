# Hytale Modding: Project Setup Reference

This document defines the technical standards for initializing and structuring Hytale Java-based plugins.

## 1. Technical Requirements
- **Java Version:** OpenJDK 25 (Standard for 2026 builds).
- **Build System:** Gradle 9.2+ (Kotlin DSL preferred).
- **Core API:** `hytale-api.jar` (provided by the Hytale SDK).

## 2. Project Folder Hierarchy
All plugins must follow the standard Gradle structure to ensure compatibility with Hytale's server-side loader.

```text
ProjectRoot/
├── src/
│   └── main/
│       ├── java/
│       │   └── [package_path]/
│       │       └── EntryPoint.java      <-- Extends JavaPlugin
│       └── resources/
│           ├── manifest.json            <-- Mandatory Metadata
│           ├── server.lang              <-- Localization
│           └── assets/                  <-- (Optional) Resource Pack
│               ├── common/              <-- Models/Textures
│               └── server/              <-- Item/Block Definitions
├── build.gradle.kts                     <-- Build Configuration
└── settings.gradle.kts                  <-- Project Name Definition
```

## 3. Configuration Formats

### manifest.json (Plugin Metadata)
The `manifest.json` file is the source of truth for the Hytale mod loader.

```json
{
  "Id": "com.organization.modname",
  "Name": "Display Name",
  "Version": "1.0.0",
  "Authors": [
    {
      "Name": "AuthorName",
      "Website": "https://example.com"
    }
  ],
  "Main": "com.organization.modname.MainClass",
  "Description": "Technical description of the plugin functionality.",
  "ServerVersion": "*",
  "IncludesAssetPack": true,
  "Dependencies": {},
  "OptionalDependencies": {},
  "DisabledByDefault": false
}
```

### build.gradle.kts (Kotlin DSL)
Essential configuration for the Hytale development environment.

```kotlin
plugins {
    java
    id("com.github.johnrengelman.shadow") version "8.1.1" // For bundling dependencies
}

group = "com.organization"
version = "1.0.0"

repositories {
    mavenCentral()
    maven("https://maven.hytale.com/releases") // Official Hytale Maven
}

dependencies {
    compileOnly("com.hypixel.hytale:hytale-api:1.0.0")
}

tasks.withType<JavaCompile> {
    options.release.set(25)
}
```

## 4. Basic Java Architecture

### The Entry Point
Every Hytale plugin must have a main class that inherits from the API's base plugin class.

```java
package com.organization.modname;

import com.hypixel.hytale.plugin.JavaPlugin;

public class MainClass extends JavaPlugin {
    
    @Override
    public void onEnable() {
        // Initialization logic: register events, commands, data handlers
        getLogger().info("Plugin successfully enabled.");
    }

    @Override
    public void onDisable() {
        // Cleanup logic: save data, close connections
        getLogger().info("Plugin disabled.");
    }
}
```

## 5. Initialization Commands
To initialize a project from scratch, use the following sequence:

1. **Create Directory:** `mkdir MyMod && cd MyMod`
2. **Init Gradle:** `gradle init --type java-library --dsl kotlin`
3. **Setup Folders:** `mkdir -p src/main/resources/assets/common`
4. **Build Plugin:** `./gradlew build`
5. **Clean Build:** `./gradlew clean build`

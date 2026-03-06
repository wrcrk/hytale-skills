# Hytale Modding Skills

A collection of structured, domain-specific modules and technical references designed to guide AI agents in developing Hytale mods. This repository provides instructions, mandates, and examples for various aspects of Hytale's modding API, focusing on Java-based server-side development and data-driven configuration.

## Installation

To add these skills to your local environment, use the `npx skills add` command followed by the path to the specific module:

```bash
npx skills add wrcrk/hytale-skills/<module-name>
```

## Project Structure

The project is organized into several "skill" modules. You can install them individually:

- **`hytale-project-setup`**: Standards for Hytale mod directory structures and Gradle configurations.
  - `npx skills add wrcrk/hytale-skills/hytale-project-setup`
- **`hytale-ecs-core`**: Core Entity Component System (ECS) principles, component definitions, and system architectures.
  - `npx skills add wrcrk/hytale-skills/hytale-ecs-core`
- **`hytale-server-logic`**: Server-side event handling, plugin lifecycles, and backend logic patterns.
  - `npx skills add wrcrk/hytale-skills/hytale-server-logic`
- **`hytale-npc-modding`**: NPC definition (JSON), AI behavior trees, and model configurations.
  - `npx skills add wrcrk/hytale-skills/hytale-npc-modding`
- **`hytale-custom-ui`**: Development of custom user interfaces using Hytale's UI markup and styling systems.
  - `npx skills add wrcrk/hytale-skills/hytale-custom-ui`
- **`hytale-interactions-media`**: Interaction frameworks, sound systems, and media handling.
  - `npx skills add wrcrk/hytale-skills/hytale-interactions-media`
- **`hytale-world-gen`**: Procedural world generation, biome configuration, and the prefab placement system.
  - `npx skills add wrcrk/hytale-skills/hytale-world-gen`

## Key Technical Mandates

Across all modules, several core principles are enforced:

- **Technology Stack**: Based on Java 25 and Hytale's proprietary ECS.
- **Modularity**: Logic is strictly separated into Components (data) and Systems (behavior).
- **Data-Driven Design**: Heavy use of JSON and specialized markup for defining entities, UI, and world features.
- **Event Handling**: Performance-critical logic uses the `EntityEventSystem`, while high-level lifecycles use the `EventBus`.

## Getting Started

Each directory is a self-contained "skill" that can be used to inform an AI agent about a specific modding domain.

1. Review the `SKILL.md` in a specific directory to understand the mandates and examples for that domain.
2. Consult the `references/` folder for API details and technical specifications.
3. Follow the standards in `hytale-project-setup` when initializing a new Hytale mod project.

## Contributing

When adding new modules:
- Maintain the consistent directory structure: `module-name/SKILL.md` and `module-name/references/`.
- Ensure `SKILL.md` includes clear Mandates, Patterns, and Examples.
- Reference docs should provide technical depth (API tables, JSON schemas, etc.).

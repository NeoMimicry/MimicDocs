# Introduction to MimicAPI

**MimicAPI** is a modding library for the game **Mimesis**. It provides a high-level API for working with game objects, players, rooms, and other game components, making mod development easier and more intuitive.

## Key Features

- 🎮 **PlayerAPI** - Player management (local player, player search, inventory)
- 🏠 **RoomAPI** - Game room management
- 👾 **ActorAPI** - Actor management (monsters, NPCs, objects)
- 🎯 **LootAPI** - Loot search and management
- 🌐 **ServerNetworkAPI** - Server networking functions
- 🔧 **ManagerAPI** - Access to game managers
- 🔍 **ReflectionHelper** - Helper methods for reflection

## Technologies

- **.NET Standard 2.1**
- **Unity Engine**
- **MelonLoader**
- **FishNet Networking**

## Quick Start

1. Download MimicAPI.dll from the [GitHub repository](https://github.com/NeoMimicry/MimicAPI)
2. Add the DLL to your mod project references
3. Add using directives:

```csharp
using MimicAPI.GameAPI;
```

4. Use the API in your code:

```csharp
// Get local player
var player = PlayerAPI.GetLocalPlayer();

// Get current room
var room = RoomAPI.GetCurrentRoom();

// Find nearest loot
var loot = LootAPI.GetNearestLoot();
```

## Documentation Structure

- **Getting Started** - How to integrate MimicAPI into your mod project
- **API Reference** - Detailed documentation for all APIs
- **Examples** - Usage examples and code samples

## License

This project is distributed under the license specified in the repository.

---

Start with the [Getting Started](./getting-started.md) section to set up your project.

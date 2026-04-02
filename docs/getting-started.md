---
sidebar_position: 2
---

# Getting Started

## Requirements

- Visual Studio 2022+ or JetBrains Rider
- .NET SDK (netstandard2.1)
- MelonLoader installed in your Mimesis game folder

## Add MimicAPI to your project

Download `MimicAPI.dll` from [GitHub Releases](https://github.com/NeoMimicry/MimicAPI/releases) and add it as a reference in your `.csproj`:

```xml
<ItemGroup>
  <Reference Include="MimicAPI">
    <HintPath>libs\MimicAPI.dll</HintPath>
  </Reference>
</ItemGroup>
```

## Create your mod

```csharp
using MelonLoader;
using MimicAPI.GameAPI;

[assembly: MelonInfo(typeof(MyMod.Mod), "My Mod", "1.0.0", "YourName")]
[assembly: MelonGame("ReLUGames", "MIMESIS")]

namespace MyMod
{
    public class Mod : MelonMod
    {
        public override void OnInitializeMelon()
        {
            MelonLogger.Msg("Mod loaded!");
        }
    }
}
```

## Wait for the game to be ready

Most APIs return `null` until the game session is fully loaded. A safe pattern is to check `CoreAPI` before doing anything:

```csharp
bool IsReady() =>
    CoreAPI.GetHub() != null &&
    CoreAPI.GetVWorld() != null &&
    CoreAPI.GetVRoomManager() != null;

public override void OnUpdate()
{
    if (!IsReady()) return;

    var room = RoomAPI.GetCurrentRoom();
    if (room == null) return;

    // safe to use APIs here
}
```

## Working with returned objects

Server-side types (`IVroom`, `VPlayer`, `VActor`) are returned as `object` because they can't be referenced directly from a mod. Use `ReflectionHelper` to read fields on them:

```csharp
var room = RoomAPI.GetCurrentRoom();
if (room == null) return;

// RoomAPI wraps the common fields for you
int day = RoomAPI.GetCurrentGameDay(room);
int members = RoomAPI.GetMemberCount(room);

// For anything not wrapped, use ReflectionHelper directly
long tick = ReflectionHelper.GetFieldValue<long>(room, "_currentTick");
```

## Troubleshooting

**Everything returns null** — the game isn't fully loaded yet. Add the `IsReady()` check above.

**MissingMethodException** — a game update changed an internal method name. Check the [ReflectionHelper](./api/reflection.md) page for how to find the new name using Fish AI Reader.

**CS1503 / type mismatch** — don't try to cast returned `object` values to game types like `IVroom`. Keep them as `object` and pass them back into MimicAPI methods.

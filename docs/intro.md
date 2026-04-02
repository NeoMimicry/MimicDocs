---
slug: /
sidebar_position: 1
---

# MimicAPI

MimicAPI is a modding library for **Mimesis** built on top of MelonLoader. It wraps the game's internal server-side types — which aren't directly accessible from mods — and exposes them through a clean, reflection-based API.

## What's included

| API | What it does |
|-----|-------------|
| [CoreAPI](./api/core.md) | Access to `Hub`, `VWorld`, and `VRoomManager` |
| [ManagerAPI](./api/manager.md) | All game managers (`DataManager`, `UIManager`, etc.) |
| [PlayerAPI](./api/player.md) | Local and remote players via `ProtoActor` |
| [RoomAPI](./api/room.md) | Game rooms — waiting, maintenance, death match |
| [ActorAPI](./api/actor.md) | Server-side actors — players, monsters, loot objects |
| [LootAPI](./api/loot.md) | Loot objects in the Unity scene |
| [ServerNetworkAPI](./api/server-network.md) | Sessions, connections, server state |
| [ReflectionHelper](./api/reflection.md) | Low-level field/method access for anything not yet wrapped |

## Why reflection?

Mimesis runs server logic in `Assembly-CSharp` using types like `IVroom`, `VPlayer`, and `VActor`. These types are not exposed to mods as typed references — MimicAPI uses reflection to reach them at runtime and returns them as `object`. This means you can use every API without needing game assembly stubs in your project.

## Quick example

```csharp
using MelonLoader;
using MimicAPI.GameAPI;

public class MyMod : MelonMod
{
    public override void OnUpdate()
    {
        var room = RoomAPI.GetCurrentRoom();
        if (room == null) return;

        MelonLogger.Msg($"Room: {RoomAPI.GetRoomName(room)}");
        MelonLogger.Msg($"Day:  {RoomAPI.GetCurrentGameDay(room)}");
        MelonLogger.Msg($"Players: {RoomAPI.GetRoomPlayers(room).Count}");
    }
}
```

Ready to set up? Head to [Getting Started](./getting-started.md).

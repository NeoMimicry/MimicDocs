---
sidebar_position: 4
---

# RoomAPI

RoomAPI gives you access to the game's server-side rooms. Rooms are returned as `object` because `IVroom` is a game type that can't be referenced directly from a mod — just pass them back into RoomAPI methods and everything works.

## Room types

The game has five room types:

| `GetRoomName()` | `GetRoomType()` | Description |
|-----------------|-----------------|-------------|
| `VWaitingRoom` | `Waiting` | Lobby before a run |
| `MaintenanceRoom` | `Maintenance` | Between-run maintenance phase |
| `DeathMatchRoom` | `DeathMatch` | PvP arena |
| *(game room)* | `Game` | Active dungeon run |
| — | `Invalid` | Not assigned |

## Getting rooms

### GetCurrentRoom()

```csharp
public static object? GetCurrentRoom()
```

Returns the first playable room. This is the room the local player is actively in.

```csharp
var room = RoomAPI.GetCurrentRoom();
if (room == null) return;

MelonLogger.Msg($"In {RoomAPI.GetRoomName(room)}, day {RoomAPI.GetCurrentGameDay(room)}");
```

### GetAllRooms()

```csharp
public static object[] GetAllRooms()
```

All rooms currently tracked by `VRoomManager`.

### GetAllPlayableRooms()

```csharp
public static List<object> GetAllPlayableRooms()
```

Filtered to rooms where `IsPlayable()` returns true.

### GetRoom()

```csharp
public static object? GetRoom(long roomID)
```

Look up a specific room by its ID.

### GetAllRoomIDs()

```csharp
public static List<long> GetAllRoomIDs()
```

### RoomExists()

```csharp
public static bool RoomExists(long roomID)
```

## Room identity

### GetRoomID()

```csharp
public static long GetRoomID(object? room)
```

### GetRoomMasterID()

```csharp
public static int GetRoomMasterID(object? room)
```

### GetRoomName()

```csharp
public static string GetRoomName(object? room)
```

Returns the runtime class name: `VWaitingRoom`, `MaintenanceRoom`, `DeathMatchRoom`, etc.

### GetRoomType()

```csharp
public static object? GetRoomType(object? room)
```

Returns the `VRoomType` enum value as `object`. Compare with `.ToString()`:

```csharp
string type = RoomAPI.GetRoomType(room)?.ToString();
if (type == "Waiting")
    MelonLogger.Msg("We are in the lobby");
```

### GetRoomProperty()

```csharp
public static object? GetRoomProperty(object? room)
```

Returns the `IVRoomProperty` object. Use `ReflectionHelper` to read `SessionID`, `vRoomType`, or `TargetCurrency` from it.

## Room state

### IsRoomPlayable()

```csharp
public static bool IsRoomPlayable(object? room)
```

### GetCurrentGameDay()

```csharp
public static int GetCurrentGameDay(object? room)
```

### GetCurrentSessionCycle()

```csharp
public static int GetCurrentSessionCycle(object? room)
```

### GetCurrentTick()

```csharp
public static long GetCurrentTick(object? room)
```

### GetMemberCount()

```csharp
public static int GetMemberCount(object? room)
```

### GetDeadPlayerCount()

```csharp
public static int GetDeadPlayerCount(object? room)
```

### IsAllPlayerDead()

```csharp
public static bool IsAllPlayerDead(object? room)
```

### IsAllPlayerWastedOrDead()

```csharp
public static bool IsAllPlayerWastedOrDead(object? room)
```

## Players and actors

### GetRoomPlayers()

```csharp
public static List<object> GetRoomPlayers(object? room)
```

Server-side `VPlayer` objects from `_vPlayerDict`. Use `ReflectionHelper` to read fields on them, or pass them to ActorAPI.

### GetRoomActors()

```csharp
public static List<object> GetRoomActors(object? room)
```

All actors from `_vActorDict` — players, monsters, loot objects, etc.

### GetRoomLevelObjects()

```csharp
public static IDictionary? GetRoomLevelObjects(object? room)
```

## Currency

### GetRoomCurrency()

```csharp
public static int GetRoomCurrency(object? room)
```

### GetContaRecoveryRate()

```csharp
public static long GetContaRecoveryRate(object? room)
```

## Network helpers

### GetRoomPlayerCount()

```csharp
public static int GetRoomPlayerCount(object? room)
```

### GetRoomPlayerDictionary()

```csharp
public static IDictionary? GetRoomPlayerDictionary(object? room)
```

## Full example

```csharp
public override void OnUpdate()
{
    if (!Input.GetKeyDown(KeyCode.F2)) return;

    var rooms = RoomAPI.GetAllRooms();
    MelonLogger.Msg($"--- {rooms.Length} rooms ---");

    foreach (var room in rooms)
    {
        string name    = RoomAPI.GetRoomName(room);
        string type    = RoomAPI.GetRoomType(room)?.ToString() ?? "?";
        long id        = RoomAPI.GetRoomID(room);
        int members    = RoomAPI.GetMemberCount(room);
        int day        = RoomAPI.GetCurrentGameDay(room);
        bool playable  = RoomAPI.IsRoomPlayable(room);

        MelonLogger.Msg($"[{type}] {name} id={id} members={members} day={day} playable={playable}");
    }
}
```

:::warning
All methods return safe defaults (`null`, `0`, empty collections) when passed `null`. Always null-check `GetCurrentRoom()` before use.
:::

## See also

- [ActorAPI](./actor.md) — monsters, players, and loot inside a room
- [ServerNetworkAPI](./server-network.md) — session and connection info

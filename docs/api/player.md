---
sidebar_position: 3
---

# PlayerAPI

PlayerAPI works with client-side players via `ProtoActor` — Unity MonoBehaviour objects you can interact with directly. This is different from ActorAPI which works with server-side player representations inside rooms.

## Getting players

### GetLocalPlayer()

```csharp
public static ProtoActor? GetLocalPlayer()
```

Returns the player on this client. The most common starting point for player logic.

```csharp
var player = PlayerAPI.GetLocalPlayer();
if (player == null) return;

MelonLogger.Msg($"Playing as: {PlayerAPI.GetPlayerName(player)}");
MelonLogger.Msg($"Position: {PlayerAPI.GetPlayerPosition(player)}");
```

### GetAllPlayers()

```csharp
public static ProtoActor[]? GetAllPlayers()
```

Every `ProtoActor` in the scene, including dead players.

### GetOtherPlayers()

```csharp
public static ProtoActor[]? GetOtherPlayers()
```

All players except the local player.

### GetPlayerByName()

```csharp
public static ProtoActor? GetPlayerByName(string name)
```

Case-insensitive name search.

### GetPlayerByID()

```csharp
public static ProtoActor? GetPlayerByID(uint actorID)
```

## Checking state

### HasLocalPlayer()

```csharp
public static bool HasLocalPlayer()
```

Quick null-check before doing anything player-related.

### IsPlayerAlive() / IsLocalPlayerAlive()

```csharp
public static bool IsPlayerAlive(ProtoActor? actor)
public static bool IsLocalPlayerAlive()
```

Calls `IsAliveStatus()` on the player's `StatManager`.

```csharp
if (!PlayerAPI.IsLocalPlayerAlive())
{
    MelonLogger.Msg("You are dead");
    return;
}
```

### IsPlayerValid()

```csharp
public static bool IsPlayerValid(ProtoActor? actor)
```

Returns `true` if the actor is not null and its GameObject is active in the scene.

## Position

### GetPlayerPosition() / GetLocalPlayerPosition()

```csharp
public static Vector3 GetPlayerPosition(ProtoActor? actor)
public static Vector3 GetLocalPlayerPosition()
```

Returns `Vector3.zero` if the actor is null.

### GetDistanceBetweenPlayers()

```csharp
public static float GetDistanceBetweenPlayers(ProtoActor? actor1, ProtoActor? actor2)
```

## Finding nearby players

### GetPlayersInRange()

```csharp
public static List<ProtoActor> GetPlayersInRange(float range, Vector3? center = null)
```

`center` defaults to the local player position if not provided.

```csharp
var nearby = PlayerAPI.GetPlayersInRange(20f);
MelonLogger.Msg($"{nearby.Count} players within 20m");
```

### GetAlivePlayersInRange()

```csharp
public static List<ProtoActor> GetAlivePlayersInRange(float range, Vector3? center = null)
```

### GetNearestPlayer() / GetNearestAlivePlayer()

```csharp
public static ProtoActor? GetNearestPlayer(Vector3? center = null)
public static ProtoActor? GetNearestAlivePlayer(Vector3? center = null)
```

```csharp
var nearest = PlayerAPI.GetNearestAlivePlayer();
if (nearest != null)
{
    float dist = PlayerAPI.GetDistanceBetweenPlayers(PlayerAPI.GetLocalPlayer(), nearest);
    MelonLogger.Msg($"{PlayerAPI.GetPlayerName(nearest)} is {dist:F1}m away");
}
```

## Components

### GetStatManager() / GetLocalStatManager()

```csharp
public static StatManager? GetStatManager(ProtoActor? actor)
public static StatManager? GetLocalStatManager()
```

### GetMovementController() / GetLocalMovementController()

```csharp
public static MovementController? GetMovementController(ProtoActor? actor)
public static MovementController? GetLocalMovementController()
```

### GetInventory() / GetLocalInventory()

```csharp
public static object? GetInventory(ProtoActor? actor)
public static object? GetLocalInventory()
```

Returns the inventory as `object`. Use `ReflectionHelper` to read fields on it.

### GetInventoryItems()

```csharp
public static List<InventoryItem> GetInventoryItems(ProtoActor? actor)
```

### GetPlayerName()

```csharp
public static string GetPlayerName(ProtoActor? actor)
```

Returns the player GameObject name, or `"Unknown"` if null.

## Full example

```csharp
public override void OnUpdate()
{
    if (!Input.GetKeyDown(KeyCode.F1)) return;

    var players = PlayerAPI.GetAllPlayers();
    if (players == null) return;

    foreach (var p in players)
    {
        string name = PlayerAPI.GetPlayerName(p);
        Vector3 pos  = PlayerAPI.GetPlayerPosition(p);
        bool alive   = PlayerAPI.IsPlayerAlive(p);
        bool local   = p.AmIAvatar();

        MelonLogger.Msg($"{(local ? "[YOU] " : "")}{name} — {pos} — {(alive ? "alive" : "dead")}");
    }
}
```

## See also

- [ActorAPI](./actor.md) — server-side player representations inside rooms
- [RoomAPI](./room.md) — the room the player is in

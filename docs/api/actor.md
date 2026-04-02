---
sidebar_position: 5
---

# ActorAPI

ActorAPI works with server-side actors inside a room — players (`VPlayer`), monsters (`VMonster`), and loot objects (`VLootingObject`). All actors are returned as `object` since these types can't be referenced directly from a mod.

:::info ProtoActor vs VPlayer
`ProtoActor` (PlayerAPI) is the client-side Unity object you move around. `VPlayer` (ActorAPI) is the server-side representation inside a room. They represent the same player but are different objects used for different purposes.
:::

## Players

### GetAllVPlayersInRoom()

```csharp
public static List<object> GetAllVPlayersInRoom(object? room)
```

All server-side players in the room.

```csharp
var room = RoomAPI.GetCurrentRoom();
var players = ActorAPI.GetAllVPlayersInRoom(room);
MelonLogger.Msg($"{players.Count} players in room");
```

### GetVPlayerInRoom()

```csharp
public static object? GetVPlayerInRoom(object? room, int actorID)
```

Find a specific player by their `ObjectID`.

### GetAlivePlayersInRoom()

```csharp
public static List<object> GetAlivePlayersInRoom(object? room)
```

### GetDeadPlayersInRoom()

```csharp
public static List<object> GetDeadPlayersInRoom(object? room)
```

### HasAlivePlayersInRoom()

```csharp
public static bool HasAlivePlayersInRoom(object? room)
```

## Actors

### GetAllVActorsInRoom()

```csharp
public static List<object> GetAllVActorsInRoom(object? room)
```

Every actor in the room — players, monsters, loot objects, projectiles, etc.

### GetVActorInRoom()

```csharp
public static object? GetVActorInRoom(object? room, int actorID)
```

## Monsters

### GetMonstersInRoom()

```csharp
public static List<object> GetMonstersInRoom(object? room)
```

Actors whose runtime type name is `VMonster`.

### GetAliveMonstersInRoom()

```csharp
public static List<object> GetAliveMonstersInRoom(object? room)
```

### GetDeadMonstersInRoom()

```csharp
public static List<object> GetDeadMonstersInRoom(object? room)
```

### HasAliveMonstersInRoom()

```csharp
public static bool HasAliveMonstersInRoom(object? room)
```

```csharp
var room = RoomAPI.GetCurrentRoom();
if (ActorAPI.HasAliveMonstersInRoom(room))
    MelonLogger.Msg("Monsters are still alive");
else
    MelonLogger.Msg("Room cleared");
```

## Loot objects

### GetLootingObjectsInRoom()

```csharp
public static List<object> GetLootingObjectsInRoom(object? room)
```

Actors whose runtime type name is `VLootingObject`.

## Custom type filter

### GetActorsByTypeName()

```csharp
public static List<object> GetActorsByTypeName(object? room, string typeName)
```

Filter by any runtime type name. Useful for types not covered by the named methods.

```csharp
var room = RoomAPI.GetCurrentRoom();
var projectiles = ActorAPI.GetActorsByTypeName(room, "VProjectileObject");
var fieldSkills = ActorAPI.GetActorsByTypeName(room, "FieldSkillObject");
```

## Reading actor data

Actors are `object`, so use `ReflectionHelper` to access their fields:

```csharp
var room = RoomAPI.GetCurrentRoom();
var actors = ActorAPI.GetAllVActorsInRoom(room);

foreach (var actor in actors)
{
    int id   = ReflectionHelper.GetFieldValue<int>(actor, "ObjectID");
    bool alive = ReflectionHelper.InvokeMethod(actor, "IsAliveStatus") is bool b && b;

    MelonLogger.Msg($"{actor.GetType().Name} #{id} — {(alive ? "alive" : "dead")}");
}
```

## Full example — room status

```csharp
public override void OnUpdate()
{
    if (!Input.GetKeyDown(KeyCode.F3)) return;

    var room = RoomAPI.GetCurrentRoom();
    if (room == null) return;

    var alivePlayers  = ActorAPI.GetAlivePlayersInRoom(room);
    var deadPlayers   = ActorAPI.GetDeadPlayersInRoom(room);
    var aliveMonsters = ActorAPI.GetAliveMonstersInRoom(room);
    var loot          = ActorAPI.GetLootingObjectsInRoom(room);

    MelonLogger.Msg($"Players:  {alivePlayers.Count} alive / {deadPlayers.Count} dead");
    MelonLogger.Msg($"Monsters: {aliveMonsters.Count} alive");
    MelonLogger.Msg($"Loot objects: {loot.Count}");

    if (ActorAPI.IsAllPlayerDead(room) ?? RoomAPI.IsAllPlayerDead(room))
        MelonLogger.Msg("All players are dead — run over");
}
```

## See also

- [RoomAPI](./room.md) — getting the room to pass in
- [PlayerAPI](./player.md) — client-side player control
- [ReflectionHelper](./reflection.md) — reading fields on returned objects

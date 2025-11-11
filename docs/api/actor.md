# ActorAPI

**ActorAPI** provides methods for working with actors in game rooms: players (VPlayer), monsters (VMonster), and loot objects (VLootingObject).

## VPlayer Methods

### GetVPlayerInRoom()

Gets a VPlayer by Actor ID in a specified room.

```csharp
public static VPlayer? GetVPlayerInRoom(IVroom? room, int actorID)
```

### GetAllVPlayersInRoom()

Gets all VPlayers in a room.

```csharp
public static List<VPlayer> GetAllVPlayersInRoom(IVroom? room)
```

### GetAlivePlayersInRoom()

Gets all alive players in a room.

```csharp
public static List<VPlayer> GetAlivePlayersInRoom(IVroom? room)
```

### GetDeadPlayersInRoom()

Gets all dead players in a room.

```csharp
public static List<VPlayer> GetDeadPlayersInRoom(IVroom? room)
```

## VActor Methods

### GetAllVActorsInRoom()

Gets all actors in a room.

```csharp
public static List<VActor> GetAllVActorsInRoom(IVroom? room)
```

### GetVActorInRoom()

Gets an actor by ID in a room.

```csharp
public static VActor? GetVActorInRoom(IVroom? room, int actorID)
```

## Monster Methods

### GetMonstersInRoom()

Gets all monsters in a room.

```csharp
public static List<VActor> GetMonstersInRoom(IVroom? room)
```

### GetAliveMonstersInRoom()

Gets all alive monsters in a room.

```csharp
public static List<VActor> GetAliveMonstersInRoom(IVroom? room)
```

### GetDeadMonstersInRoom()

Gets all dead monsters in a room.

```csharp
public static List<VActor> GetDeadMonstersInRoom(IVroom? room)
```

## Loot Object Methods

### GetLootingObjectsInRoom()

Gets all loot objects in a room.

```csharp
public static List<VActor> GetLootingObjectsInRoom(IVroom? room)
```

## Generic Methods

### GetActorsByType\<T\>()

Gets all actors of a specific type in a room.

```csharp
public static List<VActor> GetActorsByType<T>(IVroom? room) where T : VActor
```

**Example:**
```csharp
var room = RoomAPI.GetCurrentRoom();

// Get all VMonster
var monsters = ActorAPI.GetActorsByType<VMonster>(room);

// Get all VLootingObject
var lootObjects = ActorAPI.GetActorsByType<VLootingObject>(room);
```

## Check Methods

### HasAliveMonstersInRoom()

Checks if there are alive monsters in a room.

```csharp
public static bool HasAliveMonstersInRoom(IVroom? room)
```

### HasAlivePlayersInRoom()

Checks if there are alive players in a room.

```csharp
public static bool HasAlivePlayersInRoom(IVroom? room)
```

## Practical Examples

### Display Room Statistics

```csharp
void DisplayRoomStats()
{
    var room = RoomAPI.GetCurrentRoom();
    if (room == null)
    {
        MelonLogger.Warning("Room not found");
        return;
    }

    var allPlayers = ActorAPI.GetAllVPlayersInRoom(room);
    var alivePlayers = ActorAPI.GetAlivePlayersInRoom(room);
    var deadPlayers = ActorAPI.GetDeadPlayersInRoom(room);
    
    var allMonsters = ActorAPI.GetMonstersInRoom(room);
    var aliveMonsters = ActorAPI.GetAliveMonstersInRoom(room);
    var deadMonsters = ActorAPI.GetDeadMonstersInRoom(room);
    
    var lootObjects = ActorAPI.GetLootingObjectsInRoom(room);

    MelonLogger.Msg("=== Room Statistics ===");
    MelonLogger.Msg($"Players: {alivePlayers.Count} alive / {deadPlayers.Count} dead / {allPlayers.Count} total");
    MelonLogger.Msg($"Monsters: {aliveMonsters.Count} alive / {deadMonsters.Count} dead / {allMonsters.Count} total");
    MelonLogger.Msg($"Loot objects: {lootObjects.Count}");
}
```

### Check Wave Completion

```csharp
bool IsWaveCompleted()
{
    var room = RoomAPI.GetCurrentRoom();
    if (room == null) return false;

    bool hasAliveMonsters = ActorAPI.HasAliveMonstersInRoom(room);
    bool hasAlivePlayers = ActorAPI.HasAlivePlayersInRoom(room);

    // Wave completed if no alive monsters and there are alive players
    return !hasAliveMonsters && hasAlivePlayers;
}

public override void OnUpdate()
{
    if (IsWaveCompleted())
    {
        MelonLogger.Msg("Wave completed! All monsters eliminated!");
    }
}
```

## Notes

:::warning Important
- ActorAPI works with server-side actor representations (VPlayer, VActor)
- Don't confuse with ProtoActor from PlayerAPI - these are different types
- Always check `room` for `null` before using
:::

:::tip Tip
- Use ActorAPI for server logic and statistics
- For working with local players, use PlayerAPI
- Cache results if calling methods frequently
:::

## Differences: VPlayer vs ProtoActor

| Aspect | VPlayer (ActorAPI) | ProtoActor (PlayerAPI) |
|--------|-------------------|----------------------|
| Scope | Server room logic | Client game objects |
| Component Access | Limited | Full (Transform, Unity components) |
| Use Case | Statistics, room logic | Player control, position, inventory |

## See Also

- [PlayerAPI](./player.md) - Working with local players (ProtoActor)
- [RoomAPI](./room.md) - Room management
- [LootAPI](./loot.md) - Working with loot

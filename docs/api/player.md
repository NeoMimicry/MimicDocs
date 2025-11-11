# PlayerAPI

**PlayerAPI** provides convenient methods for working with players: searching, getting information, and managing state.

## Player Retrieval Methods

### GetLocalPlayer()

Gets the local player (player on the current client).

```csharp
public static ProtoActor? GetLocalPlayer()
```

**Returns:** `ProtoActor?` - Local player or `null`

**Example:**
```csharp
var player = PlayerAPI.GetLocalPlayer();
if (player != null)
{
    MelonLogger.Msg($"Local player: {player.gameObject.name}");
}
```

### GetAllPlayers()

Gets all players in the game.

```csharp
public static ProtoActor[]? GetAllPlayers()
```

**Returns:** `ProtoActor[]?` - Array of all players

### GetOtherPlayers()

Gets all players except the local player.

```csharp
public static ProtoActor[]? GetOtherPlayers()
```

### GetPlayerByName()

Search for a player by name (case-insensitive).

```csharp
public static ProtoActor? GetPlayerByName(string name)
```

### GetPlayerByID()

Gets a player by Actor ID.

```csharp
public static ProtoActor? GetPlayerByID(uint actorID)
```

## State Check Methods

### IsPlayerValid()

Checks if a player is valid (not null and active).

```csharp
public static bool IsPlayerValid(ProtoActor? actor)
```

### HasLocalPlayer()

Checks if the local player exists.

```csharp
public static bool HasLocalPlayer()
```

### IsPlayerAlive()

Checks if a player is alive.

```csharp
public static bool IsPlayerAlive(ProtoActor? actor)
```

### IsLocalPlayerAlive()

Checks if the local player is alive.

```csharp
public static bool IsLocalPlayerAlive()
```

## Component Methods

### GetStatManager()

Gets the StatManager for a player.

```csharp
public static StatManager? GetStatManager(ProtoActor? actor)
public static StatManager? GetLocalStatManager()
```

### GetMovementController()

Gets the player's movement controller.

```csharp
public static MovementController? GetMovementController(ProtoActor? actor)
public static MovementController? GetLocalMovementController()
```

### GetInventory()

Gets the player's inventory.

```csharp
public static object? GetInventory(ProtoActor? actor)
public static object? GetLocalInventory()
```

### GetInventoryItems()

Gets the list of items in the player's inventory.

```csharp
public static List<InventoryItem> GetInventoryItems(ProtoActor? actor)
```

## Position Methods

### GetPlayerPosition()

Gets a player's position.

```csharp
public static Vector3 GetPlayerPosition(ProtoActor? actor)
public static Vector3 GetLocalPlayerPosition()
```

### GetDistanceBetweenPlayers()

Calculates the distance between two players.

```csharp
public static float GetDistanceBetweenPlayers(ProtoActor? actor1, ProtoActor? actor2)
```

## Range Search Methods

### GetPlayersInRange()

Gets all players within a specified radius.

```csharp
public static List<ProtoActor> GetPlayersInRange(float range, Vector3? center = null)
```

**Example:**
```csharp
// Find all players within 10 meters
var nearbyPlayers = PlayerAPI.GetPlayersInRange(10f);
MelonLogger.Msg($"Players nearby: {nearbyPlayers.Count}");
```

### GetAlivePlayersInRange()

Gets all alive players within a specified radius.

```csharp
public static List<ProtoActor> GetAlivePlayersInRange(float range, Vector3? center = null)
```

### GetNearestPlayer()

Finds the nearest player.

```csharp
public static ProtoActor? GetNearestPlayer(Vector3? center = null)
```

### GetNearestAlivePlayer()

Finds the nearest alive player.

```csharp
public static ProtoActor? GetNearestAlivePlayer(Vector3? center = null)
```

## Additional Methods

### GetPlayerName()

Gets a player's name.

```csharp
public static string GetPlayerName(ProtoActor? actor)
```

## Practical Examples

### Display Information About All Players

```csharp
void DisplayAllPlayersInfo()
{
    var players = PlayerAPI.GetAllPlayers();
    if (players == null) return;

    MelonLogger.Msg($"=== Total Players: {players.Length} ===");
    
    foreach (var player in players)
    {
        string name = PlayerAPI.GetPlayerName(player);
        Vector3 pos = PlayerAPI.GetPlayerPosition(player);
        bool isAlive = PlayerAPI.IsPlayerAlive(player);
        bool isLocal = player.AmIAvatar();
        
        MelonLogger.Msg($"{name} - Position: {pos}, Alive: {isAlive}, Local: {isLocal}");
    }
}
```

### Find Nearest Enemy

```csharp
ProtoActor? FindNearestEnemy()
{
    var localPlayer = PlayerAPI.GetLocalPlayer();
    if (localPlayer == null) return null;

    var otherPlayers = PlayerAPI.GetOtherPlayers();
    if (otherPlayers == null || otherPlayers.Length == 0) return null;

    ProtoActor? nearest = null;
    float minDistance = float.MaxValue;

    foreach (var player in otherPlayers)
    {
        if (!PlayerAPI.IsPlayerAlive(player)) continue;

        float distance = PlayerAPI.GetDistanceBetweenPlayers(localPlayer, player);
        if (distance < minDistance)
        {
            minDistance = distance;
            nearest = player;
        }
    }

    return nearest;
}
```

### Nearby Players Notification System

```csharp
float checkRadius = 15f;
float checkInterval = 2f;
float lastCheckTime = 0f;

public override void OnUpdate()
{
    if (Time.time - lastCheckTime < checkInterval) return;
    lastCheckTime = Time.time;

    var nearbyPlayers = PlayerAPI.GetAlivePlayersInRange(checkRadius);
    
    // Exclude local player
    nearbyPlayers.RemoveAll(p => p.AmIAvatar());
    
    if (nearbyPlayers.Count > 0)
    {
        MelonLogger.Warning($"ATTENTION! {nearbyPlayers.Count} players nearby!");
        foreach (var player in nearbyPlayers)
        {
            string name = PlayerAPI.GetPlayerName(player);
            float distance = Vector3.Distance(
                PlayerAPI.GetLocalPlayerPosition(),
                PlayerAPI.GetPlayerPosition(player)
            );
            MelonLogger.Msg($"- {name} at {distance:F1}m");
        }
    }
}
```

## Notes

:::warning Important
- Many methods return `null`. Always check the result.
- `GetAllPlayers()` returns all actors, including possibly dead players.
- Use `IsPlayerAlive()` to check life status.
:::

:::tip Tips
- Use `HasLocalPlayer()` before calling other methods for safety
- Cache frequently used values for performance
- Range search methods can be expensive, use them wisely
:::

## See Also

- [ActorAPI](./actor.md) - Working with actors in rooms
- [RoomAPI](./room.md) - Room management
- [LootAPI](./loot.md) - Loot search

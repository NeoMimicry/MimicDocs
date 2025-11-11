# LootAPI

**LootAPI** provides methods for searching and working with loot (LootingLevelObject) in the game.

## Loot Retrieval Methods

### GetAllLoot()

Gets all active loot in the game.

```csharp
public static LootingLevelObject[] GetAllLoot()
```

**Example:**
```csharp
var loot = LootAPI.GetAllLoot();
MelonLogger.Msg($"Active loot: {loot.Length}");
```

### GetInactiveLoot()

Gets all inactive loot in the game.

```csharp
public static LootingLevelObject[] GetInactiveLoot()
```

## Range Search Methods

### GetLootNearby()

Gets all loot within a specified radius.

```csharp
public static LootingLevelObject[] GetLootNearby(float maxDistance, Vector3? searchCenter = null)
```

**Parameters:**
- `maxDistance` - Maximum search distance
- `searchCenter` - Search center (default: local player position)

**Example:**
```csharp
// Find loot within 20 meters of the player
var nearbyLoot = LootAPI.GetLootNearby(20f);
MelonLogger.Msg($"Loot nearby: {nearbyLoot.Length}");

// Find loot within radius from a specific point
Vector3 customPoint = new Vector3(10, 0, 10);
var lootAtPoint = LootAPI.GetLootNearby(15f, customPoint);
```

### FilterLootByDistance()

Filters loot by distance range.

```csharp
public static LootingLevelObject[] FilterLootByDistance(
    float minDistance, 
    float maxDistance, 
    Vector3? searchCenter = null
)
```

**Example:**
```csharp
// Find loot at a distance of 10 to 30 meters
var loot = LootAPI.FilterLootByDistance(10f, 30f);
MelonLogger.Msg($"Loot in range 10-30m: {loot.Length}");
```

## Nearest Loot Methods

### GetNearestLoot()

Finds the nearest loot to a specified point.

```csharp
public static LootingLevelObject? GetNearestLoot(Vector3? searchCenter = null)
```

**Example:**
```csharp
var nearestLoot = LootAPI.GetNearestLoot();
if (nearestLoot != null)
{
    float distance = LootAPI.GetDistanceToLoot(nearestLoot);
    MelonLogger.Msg($"Nearest loot at distance: {distance:F2}m");
}
```

### GetNearestLootInRange()

Finds the nearest loot within a specified radius.

```csharp
public static LootingLevelObject? GetNearestLootInRange(
    float maxDistance, 
    Vector3? searchCenter = null
)
```

## Name Search Methods

### GetLootByName()

Finds loot by name (partial match).

```csharp
public static LootingLevelObject[] GetLootByName(string name)
```

**Example:**
```csharp
// Find all loot containing "medkit" in the name
var medkits = LootAPI.GetLootByName("medkit");
MelonLogger.Msg($"Found medkits: {medkits.Length}");
```

## Check Methods

### HasLoot()

Checks if there is active loot in the game.

```csharp
public static bool HasLoot()
```

### HasLootNearby()

Checks if there is loot near a specified point.

```csharp
public static bool HasLootNearby(float maxDistance, Vector3? searchCenter = null)
```

**Example:**
```csharp
if (LootAPI.HasLootNearby(10f))
{
    MelonLogger.Msg("Loot nearby!");
}
```

## Helper Methods

### GetLootPosition()

Gets the position of a loot object.

```csharp
public static Vector3 GetLootPosition(LootingLevelObject loot)
```

### GetDistanceToLoot()

Calculates the distance to a loot object.

```csharp
public static float GetDistanceToLoot(LootingLevelObject loot, Vector3? center = null)
```

## Practical Examples

### Display All Loot on Map

```csharp
void DisplayAllLoot()
{
    var allLoot = LootAPI.GetAllLoot();
    
    MelonLogger.Msg($"=== All Loot ({allLoot.Length}) ===");
    
    foreach (var loot in allLoot)
    {
        Vector3 pos = LootAPI.GetLootPosition(loot);
        float distance = LootAPI.GetDistanceToLoot(loot);
        
        MelonLogger.Msg($"{loot.gameObject.name} - Position: {pos}, Distance: {distance:F2}m");
    }
}
```

### Nearest Loot Notification System

```csharp
float checkInterval = 1f;
float lastCheckTime = 0f;
float notificationRange = 15f;
LootingLevelObject? lastNotifiedLoot = null;

public override void OnUpdate()
{
    if (Time.time - lastCheckTime < checkInterval) return;
    lastCheckTime = Time.time;

    var nearestLoot = LootAPI.GetNearestLootInRange(notificationRange);

    if (nearestLoot != null && nearestLoot != lastNotifiedLoot)
    {
        float distance = LootAPI.GetDistanceToLoot(nearestLoot);
        MelonLogger.Msg($"LOOT NEARBY! {nearestLoot.gameObject.name} at {distance:F1}m");
        lastNotifiedLoot = nearestLoot;
    }
    else if (nearestLoot == null && lastNotifiedLoot != null)
    {
        lastNotifiedLoot = null;
    }
}
```

### Auto-collect Loot in Range

```csharp
float autoCollectRange = 5f;

public override void OnUpdate()
{
    if (Input.GetKeyDown(KeyCode.F))
    {
        AutoCollectLoot();
    }
}

void AutoCollectLoot()
{
    var nearbyLoot = LootAPI.GetLootNearby(autoCollectRange);
    
    if (nearbyLoot.Length == 0)
    {
        MelonLogger.Msg("No loot nearby");
        return;
    }

    MelonLogger.Msg($"Collecting {nearbyLoot.Length} items...");
    
    foreach (var loot in nearbyLoot)
    {
        // Loot collection logic
        MelonLogger.Msg($"Collected: {loot.gameObject.name}");
    }
}
```

### Sort Loot by Distance

```csharp
void DisplayLootByDistance()
{
    var allLoot = LootAPI.GetAllLoot();
    
    // Sort by distance
    var sortedLoot = allLoot
        .OrderBy(loot => LootAPI.GetDistanceToLoot(loot))
        .ToArray();

    MelonLogger.Msg($"=== Loot (sorted by distance) ===");
    
    for (int i = 0; i < Math.Min(10, sortedLoot.Length); i++)
    {
        var loot = sortedLoot[i];
        float distance = LootAPI.GetDistanceToLoot(loot);
        MelonLogger.Msg($"{i + 1}. {loot.gameObject.name} - {distance:F2}m");
    }
}
```

## Notes

:::warning Important
- `searchCenter` defaults to local player position
- If local player is unavailable, uses Vector3.zero
- Search methods can be expensive, use wisely
:::

:::tip Tips
- Use `HasLootNearby()` before `GetLootNearby()` for optimization
- Cache search results if using multiple times
- Set a reasonable check interval for frequent searches
:::

## Use Cases

LootAPI is useful for:
- Creating auto-collect systems
- Loot indicators on HUD
- Navigation systems to loot
- Analyzing loot distribution on map
- Valuable loot notification systems

## See Also

- [PlayerAPI](./player.md) - Working with players
- [ActorAPI](./actor.md) - Working with actors
- [RoomAPI](./room.md) - Working with rooms

---
sidebar_position: 6
---

# LootAPI

LootAPI finds and works with `LootingLevelObject` instances in the Unity scene. These are the physical loot objects placed in the world — different from server-side loot tracked by ActorAPI.

## Getting loot

### GetAllLoot()

```csharp
public static LootingLevelObject[] GetAllLoot()
```

All active (visible) loot objects in the scene.

```csharp
var loot = LootAPI.GetAllLoot();
MelonLogger.Msg($"{loot.Length} loot objects active");
```

### GetInactiveLoot()

```csharp
public static LootingLevelObject[] GetInactiveLoot()
```

Loot that exists in the scene but is currently hidden/disabled.

### GetLootByName()

```csharp
public static LootingLevelObject[] GetLootByName(string name)
```

Partial name match against the GameObject name.

```csharp
var medkits = LootAPI.GetLootByName("medkit");
```

## Finding nearby loot

### GetLootNearby()

```csharp
public static LootingLevelObject[] GetLootNearby(float maxDistance, Vector3? searchCenter = null)
```

`searchCenter` defaults to the local player position.

```csharp
var nearby = LootAPI.GetLootNearby(15f);
MelonLogger.Msg($"{nearby.Length} loot items within 15m");
```

### GetNearestLoot()

```csharp
public static LootingLevelObject? GetNearestLoot(Vector3? searchCenter = null)
```

```csharp
var nearest = LootAPI.GetNearestLoot();
if (nearest != null)
{
    float dist = LootAPI.GetDistanceToLoot(nearest);
    MelonLogger.Msg($"Nearest loot: {nearest.gameObject.name} at {dist:F1}m");
}
```

### GetNearestLootInRange()

```csharp
public static LootingLevelObject? GetNearestLootInRange(float maxDistance, Vector3? searchCenter = null)
```

Like `GetNearestLoot()` but only considers loot within `maxDistance`.

### FilterLootByDistance()

```csharp
public static LootingLevelObject[] FilterLootByDistance(float minDistance, float maxDistance, Vector3? searchCenter = null)
```

Find loot in a distance band — useful for ignoring loot that's too close or too far.

```csharp
var midRange = LootAPI.FilterLootByDistance(5f, 30f);
```

## Checking for loot

### HasLoot()

```csharp
public static bool HasLoot()
```

### HasLootNearby()

```csharp
public static bool HasLootNearby(float maxDistance, Vector3? searchCenter = null)
```

```csharp
if (LootAPI.HasLootNearby(10f))
    MelonLogger.Msg("Loot nearby!");
```

## Position and distance

### GetLootPosition()

```csharp
public static Vector3 GetLootPosition(LootingLevelObject loot)
```

### GetDistanceToLoot()

```csharp
public static float GetDistanceToLoot(LootingLevelObject loot, Vector3? center = null)
```

`center` defaults to the local player position.

## Full example — loot HUD

```csharp
public override void OnGUI()
{
    if (!LootAPI.HasLoot()) return;

    var allLoot = LootAPI.GetAllLoot()
        .OrderBy(l => LootAPI.GetDistanceToLoot(l))
        .Take(5)
        .ToArray();

    GUILayout.BeginArea(new Rect(10, 100, 280, 200));
    GUILayout.Label("Nearby loot:");

    foreach (var loot in allLoot)
    {
        float dist = LootAPI.GetDistanceToLoot(loot);
        GUILayout.Label($"  {loot.gameObject.name}  {dist:F0}m");
    }

    GUILayout.EndArea();
}
```

## See also

- [ActorAPI](./actor.md) — server-side loot objects (`VLootingObject`) inside rooms
- [PlayerAPI](./player.md) — player position used as default search center

# CoreAPI

**CoreAPI** provides access to core game components such as Hub, VWorld, VRoomManager, and PersistentData.

## Methods

### GetHub()

Gets the main Hub object of the game.

```csharp
public static Hub GetHub()
```

**Returns:**
- `Hub` - The main game hub object

**Example:**

```csharp
var hub = CoreAPI.GetHub();
if (hub != null)
{
    MelonLogger.Msg("Hub retrieved successfully!");
}
```

---

### GetVWorld()

Gets the VWorld object from Hub.

```csharp
public static VWorld? GetVWorld()
```

**Returns:**
- `VWorld?` - Virtual world object or `null` if not found

**Example:**

```csharp
var vworld = CoreAPI.GetVWorld();
if (vworld != null)
{
    MelonLogger.Msg("VWorld initialized");
}
```

---

### GetVRoomManager()

Gets the room manager from VWorld.

```csharp
public static VRoomManager? GetVRoomManager()
```

**Returns:**
- `VRoomManager?` - Room manager or `null` if not found

**Example:**

```csharp
var roomManager = CoreAPI.GetVRoomManager();
if (roomManager != null)
{
    MelonLogger.Msg("VRoomManager available");
}
```

**Usage:**

```csharp
var roomManager = CoreAPI.GetVRoomManager();
if (roomManager != null)
{
    // Can be used to get room information
    var rooms = RoomAPI.GetAllRooms();
}
```

---

### GetPersistentData()

Gets the game's persistent data (saved player data).

```csharp
public static Hub.PersistentData? GetPersistentData()
```

**Returns:**
- `Hub.PersistentData?` - Persistent data or `null` if not found

**Example:**

```csharp
var pdata = CoreAPI.GetPersistentData();
if (pdata != null)
{
    MelonLogger.Msg("Persistent data loaded");
}
```

## Practical Examples

### Checking Game Initialization

```csharp
bool IsGameInitialized()
{
    return CoreAPI.GetHub() != null && 
           CoreAPI.GetVWorld() != null && 
           CoreAPI.GetVRoomManager() != null;
}

public override void OnUpdate()
{
    if (IsGameInitialized())
    {
        // Game is fully initialized
        // Can use other APIs now
    }
}
```

### Getting Base Components on Mod Startup

```csharp
public override void OnInitializeMelon()
{
    MelonCoroutines.Start(WaitForGameInit());
}

IEnumerator WaitForGameInit()
{
    while (!IsGameInitialized())
    {
        yield return new WaitForSeconds(1f);
    }
    
    MelonLogger.Msg("Game initialized!");
    
    var hub = CoreAPI.GetHub();
    var vworld = CoreAPI.GetVWorld();
    var roomManager = CoreAPI.GetVRoomManager();
    
    // Now safe to use APIs
    InitializeMod();
}
```

## Notes

:::warning Important
All CoreAPI methods may return `null` if the game is not fully loaded yet. Always check for `null` before using the returned objects.
:::

:::tip Tip
Use CoreAPI to check game readiness before using other API modules (PlayerAPI, RoomAPI, etc.).
:::

## Dependencies

CoreAPI uses:
- **ReflectionHelper** for accessing private fields of game classes
- Classes from the `Mimic` namespace

## See Also

- [ReflectionHelper](./reflection.md) - Reflection helper methods
- [RoomAPI](./room.md) - Room management
- [ManagerAPI](./manager.md) - Access to other managers

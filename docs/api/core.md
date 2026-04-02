---
sidebar_position: 1
---

# CoreAPI

CoreAPI is the entry point for the game object hierarchy. Everything else in MimicAPI goes through these three objects.

## The object chain

```
Hub.s  (static singleton)
 └── VWorld              (<vworld>k__BackingField)
      └── VRoomManager   (_vRoomManager)
           └── _vrooms   →  all active rooms
```

## Methods

### GetHub()

```csharp
public static Hub GetHub()
```

Returns the game singleton. If this is `null`, nothing else will work — the game is not loaded yet.

```csharp
var hub = CoreAPI.GetHub();
if (hub == null)
{
    MelonLogger.Warning("Game not ready");
    return;
}
```

---

### GetVWorld()

```csharp
public static VWorld? GetVWorld()
```

Returns the virtual world that holds server state. Available once a session is running.

```csharp
if (CoreAPI.GetVWorld() != null)
    MelonLogger.Msg("Session is active");
```

---

### GetVRoomManager()

```csharp
public static VRoomManager? GetVRoomManager()
```

Returns the room manager. RoomAPI uses this internally to enumerate all rooms.

---

### GetPersistentData()

```csharp
public static Hub.PersistentData? GetPersistentData()
```

Returns the player persistent save data stored on Hub.

## Readiness check

Use this before calling any other API:

```csharp
bool IsGameReady() =>
    CoreAPI.GetHub() != null &&
    CoreAPI.GetVWorld() != null &&
    CoreAPI.GetVRoomManager() != null;

public override void OnUpdate()
{
    if (!IsGameReady()) return;
    // safe to use all APIs now
}
```

## See also

- [RoomAPI](./room.md)
- [ManagerAPI](./manager.md)

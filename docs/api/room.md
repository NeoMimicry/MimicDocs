# RoomAPI

**RoomAPI** provides methods for working with game rooms: getting rooms, room information, players and actors in rooms.

## Room Retrieval Methods

### GetRoom()

Gets a room by ID.

```csharp
public static IVroom? GetRoom(long roomID)
```

### GetCurrentRoom()

Gets the current active player room.

```csharp
public static IVroom? GetCurrentRoom()
```

**Example:**
```csharp
var room = RoomAPI.GetCurrentRoom();
if (room != null)
{
    MelonLogger.Msg($"Current room: {RoomAPI.GetRoomName(room)}");
}
```

### GetAllRooms()

Gets all existing rooms.

```csharp
public static IVroom[] GetAllRooms()
```

### GetAllPlayableRooms()

Gets all active (playable) rooms.

```csharp
public static List<IVroom> GetAllPlayableRooms()
```

### GetAllRoomsOfType\<T\>()

Gets all rooms of a specific type.

```csharp
public static IVroom[] GetAllRoomsOfType<T>() where T : IVroom
```

## Information Methods

### GetRoomID()

Gets the room ID.

```csharp
public static long GetRoomID(IVroom? room)
```

### GetRoomMasterID()

Gets the room master (host) ID.

```csharp
public static int GetRoomMasterID(IVroom? room)
```

### GetRoomName()

Gets the room name (type).

```csharp
public static string GetRoomName(IVroom? room)
```

### IsRoomPlayable()

Checks if a room is playable (active).

```csharp
public static bool IsRoomPlayable(IVroom? room)
```

### RoomExists()

Checks room existence by ID.

```csharp
public static bool RoomExists(long roomID)
```

## Game Progress Methods

### GetCurrentGameDay()

Gets the current game day in the room.

```csharp
public static int GetCurrentGameDay(IVroom? room)
```

### GetCurrentSessionCycle()

Gets the current session cycle.

```csharp
public static int GetCurrentSessionCycle(IVroom? room)
```

## Players and Actors Methods

### GetRoomPlayers()

Gets all players (VPlayer) in the room.

```csharp
public static List<VPlayer> GetRoomPlayers(IVroom? room)
```

### GetRoomActors()

Gets all actors (VActor) in the room.

```csharp
public static List<VActor> GetRoomActors(IVroom? room)
```

## Level Objects Methods

### GetRoomLevelObjects()

Gets all level objects in the room.

```csharp
public static Dictionary<int, ILevelObjectInfo>? GetRoomLevelObjects(IVroom? room)
```

## Room Settings Methods

### GetRoomMaxPlayers()

Gets the maximum number of players in the room.

```csharp
public static int GetRoomMaxPlayers(IVroom? room)
public static int GetRoomMaxPlayers(object? room)
```

### SetRoomMaxPlayers()

Sets the maximum number of players in the room.

```csharp
public static void SetRoomMaxPlayers(IVroom? room, int maxPlayers)
public static void SetRoomMaxPlayers(object? room, int maxPlayers)
```

:::warning Caution
Changing the maximum number of players can affect game balance and server logic. Use with caution.
:::

## ID List Methods

### GetAllRoomIDs()

Gets the list of IDs of all existing rooms.

```csharp
public static List<long> GetAllRoomIDs()
```

## Practical Examples

### Display Full Room Information

```csharp
void DisplayCurrentRoomInfo()
{
    var room = RoomAPI.GetCurrentRoom();
    if (room == null)
    {
        MelonLogger.Warning("Not in a room");
        return;
    }

    MelonLogger.Msg("=== Room Information ===");
    MelonLogger.Msg($"Type: {RoomAPI.GetRoomName(room)}");
    MelonLogger.Msg($"ID: {RoomAPI.GetRoomID(room)}");
    MelonLogger.Msg($"Master: {RoomAPI.GetRoomMasterID(room)}");
    MelonLogger.Msg($"Players: {RoomAPI.GetRoomPlayers(room).Count} / {RoomAPI.GetRoomMaxPlayers(room)}");
    MelonLogger.Msg($"Actors: {RoomAPI.GetRoomActors(room).Count}");
    MelonLogger.Msg($"Game day: {RoomAPI.GetCurrentGameDay(room)}");
    MelonLogger.Msg($"Session cycle: {RoomAPI.GetCurrentSessionCycle(room)}");
    MelonLogger.Msg($"Playable: {RoomAPI.IsRoomPlayable(room)}");
}
```

### Monitor All Active Rooms

```csharp
void MonitorAllRooms()
{
    var rooms = RoomAPI.GetAllRooms();
    MelonLogger.Msg($"=== Room Monitoring ({rooms.Length}) ===");

    foreach (var room in rooms)
    {
        string name = RoomAPI.GetRoomName(room);
        long id = RoomAPI.GetRoomID(room);
        int players = RoomAPI.GetRoomPlayers(room).Count;
        int maxPlayers = RoomAPI.GetRoomMaxPlayers(room);
        bool playable = RoomAPI.IsRoomPlayable(room);

        string status = playable ? "ACTIVE" : "INACTIVE";
        MelonLogger.Msg($"[{status}] {name} (ID: {id}) - {players}/{maxPlayers} players");
    }
}
```

## Notes

:::warning Important
- `GetCurrentRoom()` returns `null` if the player is not in an active room
- Always check return values for `null`
- `SetRoomMaxPlayers()` is a powerful method, use cautiously
:::

:::tip Tips
- Use `IsRoomPlayable()` to filter active rooms
- Cache `GetCurrentRoom()` if using frequently in one method
- For working with players in rooms, also use ActorAPI
:::

## See Also

- [ActorAPI](./actor.md) - Working with actors and players in rooms
- [PlayerAPI](./player.md) - Working with local players
- [ServerNetworkAPI](./server-network.md) - Server network functions

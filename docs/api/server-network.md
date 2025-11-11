# ServerNetworkAPI

**ServerNetworkAPI** provides access to server networking functions, connection management, and server component information.

:::warning Warning
This API is intended for server-side logic and may only work on the game host. Using some methods on clients may result in errors.
:::

## ServerSocket Methods

### GetServerSocket()

Gets the server socket instance.

```csharp
public static object? GetServerSocket()
```

**Example:**
```csharp
var serverSocket = ServerNetworkAPI.GetServerSocket();
if (serverSocket != null)
{
    MelonLogger.Msg("Server is active");
}
```

### IsServerRunning()

Checks if the server is running.

```csharp
public static bool IsServerRunning()
```

**Example:**
```csharp
if (ServerNetworkAPI.IsServerRunning())
{
    MelonLogger.Msg("Server is running");
}
else
{
    MelonLogger.Warning("Server is not running");
}
```

## Connection Management Methods

### GetMaximumClients()

Gets the maximum number of clients.

```csharp
public static int GetMaximumClients()
```

### SetMaximumClients()

Sets the maximum number of clients.

```csharp
public static void SetMaximumClients(object serverSocket, int value)
```

**Example:**
```csharp
var serverSocket = ServerNetworkAPI.GetServerSocket();
if (serverSocket != null)
{
    ServerNetworkAPI.SetMaximumClients(serverSocket, 16);
    MelonLogger.Msg("Client limit increased to 16");
}
```

:::danger Caution
Changing the client limit can seriously affect server performance and game balance. Use with caution.
:::

### GetCurrentClientCount()

Gets the current number of connected clients.

```csharp
public static int GetCurrentClientCount()
```

### GetAllConnectedPlayers()

Gets a list of all connected players.

```csharp
public static List<object> GetAllConnectedPlayers()
```

## Waiting Room Methods

### GetWaitingRoom()

Gets the waiting room.

```csharp
public static object? GetWaitingRoom()
```

### GetWaitingRoomMemberCount()

Gets the number of players in the waiting room.

```csharp
public static int GetWaitingRoomMemberCount()
```

### GetWaitingRoomMaxPlayers()

Gets the maximum number of players in the waiting room.

```csharp
public static int GetWaitingRoomMaxPlayers()
```

### CanPlayerEnterWaitingRoom()

Checks if a player can enter the waiting room.

```csharp
public static bool CanPlayerEnterWaitingRoom(long playerUID)
```

## Maintenance Room Methods

### GetMaintenanceRoom()

Gets the maintenance room.

```csharp
public static object? GetMaintenanceRoom()
```

### GetMaintenanceRoomMemberCount()

Gets the number of players in the maintenance room.

```csharp
public static int GetMaintenanceRoomMemberCount()
```

### GetMaintenanceRoomMaxPlayers()

Gets the maximum number of players in the maintenance room.

```csharp
public static int GetMaintenanceRoomMaxPlayers()
```

### GetRoomMemberCount()

Gets the number of members in a specified room.

```csharp
public static int GetRoomMemberCount(object room)
```

## Reflection Methods (Advanced)

### GetMaximumClientsField()

Gets FieldInfo for the maximum clients field.

```csharp
public static FieldInfo? GetMaximumClientsField()
```

### GetAllServerSocketMethods()

Gets all ServerSocket methods.

```csharp
public static IEnumerable<MethodBase> GetAllServerSocketMethods()
```

### GetVRoomManagerMethod()

Gets a VRoomManager method by name.

```csharp
public static MethodInfo? GetVRoomManagerMethod(string methodName)
```

### GetRoomMethod()

Gets a room method by type and name.

```csharp
public static MethodInfo? GetRoomMethod(string roomTypeName, string methodName)
```

## Practical Examples

### Server Monitor

```csharp
class ServerMonitor
{
    private float updateInterval = 5f;
    private float lastUpdate = 0f;

    public void Update()
    {
        if (Time.time - lastUpdate < updateInterval) return;
        lastUpdate = Time.time;

        if (!ServerNetworkAPI.IsServerRunning())
        {
            MelonLogger.Msg("Server not running");
            return;
        }

        int currentClients = ServerNetworkAPI.GetCurrentClientCount();
        int maxClients = ServerNetworkAPI.GetMaximumClients();
        int waitingRoomPlayers = ServerNetworkAPI.GetWaitingRoomMemberCount();
        
        MelonLogger.Msg("=== Server Status ===");
        MelonLogger.Msg($"Clients: {currentClients}/{maxClients}");
        MelonLogger.Msg($"In lobby: {waitingRoomPlayers}");
    }
}
```

### Automatic Server Slot Expansion

```csharp
void AutoExpandServerSlots()
{
    if (!ServerNetworkAPI.IsServerRunning()) return;

    int current = ServerNetworkAPI.GetCurrentClientCount();
    int max = ServerNetworkAPI.GetMaximumClients();

    // If more than 80% full
    float fillPercentage = (float)current / max * 100f;
    if (fillPercentage > 80f)
    {
        var serverSocket = ServerNetworkAPI.GetServerSocket();
        if (serverSocket != null)
        {
            int newMax = max + 4;
            ServerNetworkAPI.SetMaximumClients(serverSocket, newMax);
            MelonLogger.Msg($"Slots expanded: {max} -> {newMax}");
        }
    }
}
```

### Server Information Panel

```csharp
void OnGUI()
{
    if (!ServerNetworkAPI.IsServerRunning()) return;

    GUILayout.BeginArea(new Rect(10, 10, 300, 200));
    GUILayout.Label("=== Server Information ===");
    
    int current = ServerNetworkAPI.GetCurrentClientCount();
    int max = ServerNetworkAPI.GetMaximumClients();
    int waiting = ServerNetworkAPI.GetWaitingRoomMemberCount();
    int waitingMax = ServerNetworkAPI.GetWaitingRoomMaxPlayers();
    
    GUILayout.Label($"Players: {current}/{max}");
    GUILayout.Label($"In lobby: {waiting}/{waitingMax}");
    
    float fillPercentage = (float)current / max * 100f;
    GUILayout.Label($"Fill: {fillPercentage:F1}%");
    
    GUILayout.EndArea();
}
```

## Notes

:::warning Important
- Many methods only work on the host/server
- Changing the maximum client count can affect performance
- Always check `IsServerRunning()` before using other methods
:::

:::danger Security
- Don't give clients access to methods that change server settings
- Use access permission checks before changing critical parameters
- Log all server setting changes
:::

:::tip Tips
- Use ServerNetworkAPI to create admin panels
- Monitor server status regularly
- Cache results for performance optimization
:::

## Use Cases

ServerNetworkAPI is useful for:
- Creating admin panels
- Server status monitoring
- Automatic slot management
- Connection queue systems
- Server statistics

## See Also

- [RoomAPI](./room.md) - Room management
- [ManagerAPI](./manager.md) - Manager access
- [ReflectionHelper](./reflection.md) - Reflection helper methods

---
sidebar_position: 7
---

# ServerNetworkAPI

ServerNetworkAPI exposes the game's server networking layer — sessions, connections, and the two transport servers (`RUDPServer` for UDP, `SDRServer` for Steam relay). Most of this is only meaningful on the host.

## How the server is structured

```
VWorld
 ├── _rudpServer      →  RUDPServer   (UDP / LiteNetLib)
 ├── _sdrServer       →  SDRServer    (Steam relay)
 └── _sessionManager  →  SessionManager
      └── m_Contexts  →  Dictionary<long, SessionContext>  (connected players)
```

## Server state

### IsServerRunning()

```csharp
public static bool IsServerRunning()
```

Returns `true` if `VWorld` is available. Use this before calling anything else.

```csharp
if (!ServerNetworkAPI.IsServerRunning())
{
    MelonLogger.Msg("Not hosting");
    return;
}
```

### GetServerSocket()

```csharp
public static object? GetServerSocket()
```

Returns the `SDRServer` if available, otherwise the `RUDPServer`. Useful when you need to pass a socket to `SetMaximumClients`.

### GetRudpServer() / GetSdrServer()

```csharp
public static object? GetRudpServer()
public static object? GetSdrServer()
```

Access the individual transport servers directly.

## Sessions and clients

### GetSessionCount() / GetCurrentClientCount()

```csharp
public static int GetSessionCount()
public static int GetCurrentClientCount()
```

Number of active sessions from `SessionManager.m_Contexts`. Both methods return the same value.

### GetMaximumClients()

```csharp
public static int GetMaximumClients()
```

Calls `VRoomManager.GetPlayerCountInSession()`.

### SetMaximumClients()

```csharp
public static void SetMaximumClients(object serverSocket, int value)
```

Sets `_maximumClients` on the socket object.

```csharp
var socket = ServerNetworkAPI.GetServerSocket();
if (socket != null)
    ServerNetworkAPI.SetMaximumClients(socket, 16);
```

### GetAllConnectedPlayers()

```csharp
public static List<object> GetAllConnectedPlayers()
```

Returns all `SessionContext` objects from `SessionManager.m_Contexts`.

## Room helpers

These filter `RoomAPI.GetAllRooms()` by type name — no separate room lookup needed.

### GetWaitingRoom() / GetMaintenanceRoom()

```csharp
public static object? GetWaitingRoom()
public static object? GetMaintenanceRoom()
```

### GetWaitingRoomMemberCount() / GetMaintenanceRoomMemberCount()

```csharp
public static int GetWaitingRoomMemberCount()
public static int GetMaintenanceRoomMemberCount()
```

### GetWaitingRoomMaxPlayers() / GetMaintenanceRoomMaxPlayers()

```csharp
public static int GetWaitingRoomMaxPlayers()
public static int GetMaintenanceRoomMaxPlayers()
```

### CanPlayerEnterWaitingRoom()

```csharp
public static bool CanPlayerEnterWaitingRoom(long playerUID)
```

Calls `CanEnterChannel(playerUID)` on the waiting room and checks for `MsgErrorCode.Success`.

### IsPlayerInRoom()

```csharp
public static bool IsPlayerInRoom(object room, long playerUID)
```

Scans `_vPlayerDict` for a matching `UID`.

## Room player helpers

### GetRoomPlayerCount()

```csharp
public static int GetRoomPlayerCount(object? room)
```

### GetRoomPlayerDictionary()

```csharp
public static IDictionary? GetRoomPlayerDictionary(object? room)
```

## Assembly helpers

### GetGameAssembly()

```csharp
public static Assembly? GetGameAssembly()
```

Returns the `Assembly-CSharp` assembly. Useful for finding types at runtime.

### GetIVroomType() / GetGameSessionInfoType()

```csharp
public static Type? GetIVroomType()
public static Type? GetGameSessionInfoType()
```

## Full example — server status panel

```csharp
public override void OnGUI()
{
    if (!ServerNetworkAPI.IsServerRunning()) return;

    int sessions  = ServerNetworkAPI.GetSessionCount();
    int inSession = ServerNetworkAPI.GetMaximumClients();
    int waiting   = ServerNetworkAPI.GetWaitingRoomMemberCount();

    GUILayout.BeginArea(new Rect(10, 10, 250, 100));
    GUILayout.Label($"Sessions:  {sessions}");
    GUILayout.Label($"In session: {inSession}");
    GUILayout.Label($"In lobby:  {waiting}");
    GUILayout.EndArea();
}
```

:::warning
Most methods return `0` or `null` on a client that is not hosting. Always check `IsServerRunning()` first.
:::

## See also

- [RoomAPI](./room.md) — room management
- [CoreAPI](./core.md) — VWorld access

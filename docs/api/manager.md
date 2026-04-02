---
sidebar_position: 2
---

# ManagerAPI

ManagerAPI gives you access to all the game managers stored on `Hub`. Because they are C# auto-properties, their backing fields follow the `<name>k__BackingField` pattern — ManagerAPI handles that for you.

## Methods

### GetDataManager()

```csharp
public static DataManager? GetDataManager()
```

Manages game data, tables, and configuration.

---

### GetTimeUtil()

```csharp
public static TimeUtil? GetTimeUtil()
```

Time utilities used by the game loop.

---

### GetNavManager()

```csharp
public static NavManager? GetNavManager()
```

AI navigation and pathfinding.

---

### GetDynamicDataManager()

```csharp
public static DynamicDataManager? GetDynamicDataManager()
```

Dynamic session data that changes at runtime.

---

### GetUIManager()

```csharp
public static UIManager? GetUIManager()
```

All UI elements, windows, and HUD.

---

### GetCameraManager()

```csharp
public static CameraManager? GetCameraManager()
```

---

### GetAudioManager()

```csharp
public static AudioManager? GetAudioManager()
```

Sound effects, music, and audio environment.

---

### GetInputManager()

```csharp
public static InputManager? GetInputManager()
```

---

### GetNetworkManager()

```csharp
public static NetworkManagerV2? GetNetworkManager()
```

Network connections and synchronization.

---

### GetAPIHandler()

```csharp
public static APIRequestHandler? GetAPIHandler()
```

---

### GetLocalisationManager()

```csharp
public static L10NManager? GetLocalisationManager()
```

---

### GetManager\<T\>()

Generic fallback for any manager not listed above.

```csharp
public static T? GetManager<T>(string fieldName) where T : class
```

Pass the exact Hub field name. For auto-properties use `<name>k__BackingField`, for plain fields use the name directly.

```csharp
var custom = ManagerAPI.GetManager<MyManager>("<mymanager>k__BackingField");
```

---

### IsManagerAvailable\<T\>()

```csharp
public static bool IsManagerAvailable<T>(string fieldName) where T : class
```

Quick null-check without fetching the object.

## Hub field reference

| Method | Hub field |
|--------|-----------|
| `GetDataManager()` | `<dataman>k__BackingField` |
| `GetTimeUtil()` | `<timeutil>k__BackingField` |
| `GetNavManager()` | `<navman>k__BackingField` |
| `GetDynamicDataManager()` | `<dynamicDataMan>k__BackingField` |
| `GetUIManager()` | `<uiman>k__BackingField` |
| `GetCameraManager()` | `<cameraman>k__BackingField` |
| `GetAudioManager()` | `<audioman>k__BackingField` |
| `GetInputManager()` | `<inputman>k__BackingField` |
| `GetNetworkManager()` | `<netman2>k__BackingField` |
| `GetAPIHandler()` | `<apihandler>k__BackingField` |
| `GetLocalisationManager()` | `lcman` *(plain field)* |

## Example

```csharp
var data = ManagerAPI.GetDataManager();
var ui   = ManagerAPI.GetUIManager();
var nav  = ManagerAPI.GetNavManager();

if (data == null || ui == null)
{
    MelonLogger.Warning("Managers not ready yet");
    return;
}

MelonLogger.Msg("All required managers available");
```

:::warning
All methods return `null` if the manager is not initialized yet. Always null-check before use.
:::

## See also

- [CoreAPI](./core.md)
- [ReflectionHelper](./reflection.md)

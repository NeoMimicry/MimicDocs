# ManagerAPI

**ManagerAPI** provides convenient access to various game managers through a unified interface.

## Universal Method

### GetManager\<T\>()

Universal method for getting any manager by field name.

```csharp
public static T? GetManager<T>(string fieldName) where T : class
```

**Parameters:**
- `fieldName` - Field name of the manager in the Hub class
- `T` - Manager type

**Example:**
```csharp
var customManager = ManagerAPI.GetManager<CustomManager>("custommgr");
```

## Specific Manager Methods

### GetDataManager()

Gets the game data manager.

```csharp
public static DataManager? GetDataManager()
```

**Usage:** DataManager manages game data, configurations, and resources.

### GetTimeUtil()

Gets the time utility.

```csharp
public static TimeUtil? GetTimeUtil()
```

### GetNavManager()

Gets the navigation manager.

```csharp
public static NavManager? GetNavManager()
```

**Usage:** NavManager handles AI navigation and pathfinding.

### GetDynamicDataManager()

Gets the dynamic data manager.

```csharp
public static DynamicDataManager? GetDynamicDataManager()
```

### GetUIManager()

Gets the user interface manager.

```csharp
public static UIManager? GetUIManager()
```

**Usage:** UIManager controls all UI elements, windows, and HUD.

### GetCameraManager()

Gets the camera manager.

```csharp
public static CameraManager? GetCameraManager()
```

### GetAudioManager()

Gets the audio manager.

```csharp
public static AudioManager? GetAudioManager()
```

**Usage:** AudioManager handles sound effects, music, and audio environment.

### GetInputManager()

Gets the input manager.

```csharp
public static InputManager? GetInputManager()
```

### GetNetworkManager()

Gets the network manager (V2).

```csharp
public static NetworkManagerV2? GetNetworkManager()
```

**Usage:** NetworkManagerV2 manages network connections and synchronization.

### GetAPIHandler()

Gets the API request handler.

```csharp
public static APIRequestHandler? GetAPIHandler()
```

## Availability Check Method

### IsManagerAvailable\<T\>()

Checks if a manager is available.

```csharp
public static bool IsManagerAvailable<T>(string fieldName) where T : class
```

**Example:**
```csharp
if (ManagerAPI.IsManagerAvailable<UIManager>("uiman"))
{
    var uiManager = ManagerAPI.GetUIManager();
    // Safe to use UIManager
}
```

## Practical Examples

### Check All Managers Availability

```csharp
void CheckAllManagers()
{
    MelonLogger.Msg("=== Manager Check ===");
    
    MelonLogger.Msg($"DataManager: {ManagerAPI.GetDataManager() != null}");
    MelonLogger.Msg($"TimeUtil: {ManagerAPI.GetTimeUtil() != null}");
    MelonLogger.Msg($"NavManager: {ManagerAPI.GetNavManager() != null}");
    MelonLogger.Msg($"DynamicDataManager: {ManagerAPI.GetDynamicDataManager() != null}");
    MelonLogger.Msg($"UIManager: {ManagerAPI.GetUIManager() != null}");
    MelonLogger.Msg($"CameraManager: {ManagerAPI.GetCameraManager() != null}");
    MelonLogger.Msg($"AudioManager: {ManagerAPI.GetAudioManager() != null}");
    MelonLogger.Msg($"InputManager: {ManagerAPI.GetInputManager() != null}");
    MelonLogger.Msg($"NetworkManager: {ManagerAPI.GetNetworkManager() != null}");
    MelonLogger.Msg($"APIHandler: {ManagerAPI.GetAPIHandler() != null}");
}
```

### Safe Manager Usage

```csharp
void SafeManagerUsage()
{
    // Always check for null
    var uiManager = ManagerAPI.GetUIManager();
    if (uiManager == null)
    {
        MelonLogger.Warning("UIManager not available");
        return;
    }

    // Now safe to use
    // uiManager.ShowMessage("Hello!");
}
```

### Initialize Modular System

```csharp
class ModSystem
{
    private DataManager? dataManager;
    private UIManager? uiManager;
    private AudioManager? audioManager;
    private NetworkManagerV2? networkManager;

    public bool Initialize()
    {
        MelonLogger.Msg("Initializing modular system...");

        dataManager = ManagerAPI.GetDataManager();
        uiManager = ManagerAPI.GetUIManager();
        audioManager = ManagerAPI.GetAudioManager();
        networkManager = ManagerAPI.GetNetworkManager();

        bool success = dataManager != null && 
                      uiManager != null && 
                      audioManager != null &&
                      networkManager != null;

        if (success)
        {
            MelonLogger.Msg("All managers loaded successfully!");
        }
        else
        {
            MelonLogger.Error("Failed to load some managers!");
        }

        return success;
    }
}
```

## Manager Table

| Manager | Purpose | Getter Method |
|---------|---------|--------------|
| DataManager | Data and configuration management | `GetDataManager()` |
| TimeUtil | Time utilities | `GetTimeUtil()` |
| NavManager | Navigation and pathfinding | `GetNavManager()` |
| DynamicDataManager | Dynamic session data | `GetDynamicDataManager()` |
| UIManager | Interface management | `GetUIManager()` |
| CameraManager | Camera control | `GetCameraManager()` |
| AudioManager | Sound management | `GetAudioManager()` |
| InputManager | Input control | `GetInputManager()` |
| NetworkManagerV2 | Network functions | `GetNetworkManager()` |
| APIRequestHandler | API request handling | `GetAPIHandler()` |

## Notes

:::warning Important
- All methods return `null` if the manager is not initialized
- Managers may be unavailable during early game loading stages
- Always check return values for `null`
:::

:::tip Tips
- Cache frequently used managers
- Use `IsManagerAvailable<T>()` for preliminary checks
- Initialize your systems after verifying required managers are available
:::

## See Also

- [CoreAPI](./core.md) - Core game components
- [ReflectionHelper](./reflection.md) - Reflection helper methods

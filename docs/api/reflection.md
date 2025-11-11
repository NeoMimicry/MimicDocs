# ReflectionHelper

**ReflectionHelper** provides convenient methods for working with reflection in C#. Used internally by other APIs to access private fields and methods of the game.

:::info
ReflectionHelper is a utility class that simplifies working with private members of game classes. Most other API methods already use it internally.
:::

## Field Methods

### GetFieldValue()

Gets the value of an object's field.

```csharp
public static object? GetFieldValue(object target, string fieldName)
public static object? GetFieldValue(Type type, string fieldName)
```

**Example:**
```csharp
var hub = CoreAPI.GetHub();
if (hub != null)
{
    var vworld = ReflectionHelper.GetFieldValue(hub, "vworld");
    MelonLogger.Msg($"VWorld retrieved: {vworld != null}");
}
```

### GetFieldValue\<T\>()

Gets a typed field value.

```csharp
public static T GetFieldValue<T>(object target, string fieldName)
public static T GetFieldValue<T>(Type type, string fieldName)
```

**Example:**
```csharp
var hub = CoreAPI.GetHub();
if (hub != null)
{
    var vworld = ReflectionHelper.GetFieldValue<VWorld>(hub, "vworld");
    if (vworld != null)
    {
        MelonLogger.Msg("VWorld successfully retrieved with type");
    }
}
```

### SetFieldValue()

Sets the value of an object's field.

```csharp
public static void SetFieldValue(object target, string fieldName, object value)
public static void SetFieldValue(Type type, string fieldName, object value)
```

**Example:**
```csharp
var room = RoomAPI.GetCurrentRoom();
if (room != null)
{
    // Change maximum player count
    ReflectionHelper.SetFieldValue(room, "_maxPlayers", 16);
    MelonLogger.Msg("Maximum players changed");
}
```

:::warning Caution
Directly changing fields can break the game's internal logic. Use with caution.
:::

## Method Methods

### InvokeMethod()

Invokes an object's method.

```csharp
public static object? InvokeMethod(object target, string methodName, params object[] parameters)
public static object? InvokeMethod(Type type, string methodName, params object[] parameters)
```

**Example:**
```csharp
var room = RoomAPI.GetCurrentRoom();
if (room != null)
{
    var result = ReflectionHelper.InvokeMethod(room, "IsPlayable");
    if (result is bool isPlayable)
    {
        MelonLogger.Msg($"Room is playable: {isPlayable}");
    }
}
```

**With parameters:**
```csharp
var waitingRoom = ServerNetworkAPI.GetWaitingRoom();
if (waitingRoom != null)
{
    long playerUid = 12345;
    var result = ReflectionHelper.InvokeMethod(waitingRoom, "CanEnterChannel", playerUid);
}
```

### InvokeMethod\<T\>()

Invokes a method with a typed return value.

```csharp
public static T? InvokeMethod<T>(object target, string methodName, params object[] parameters) where T : class
public static T? InvokeMethod<T>(Type type, string methodName, params object[] parameters) where T : class
```

## Property Methods

### GetProperty()

Gets PropertyInfo for a property.

```csharp
public static PropertyInfo? GetProperty(Type type, string propertyName)
```

### GetPropertyValue()

Gets a property value.

```csharp
public static object? GetPropertyValue(object target, string propertyName)
```

### GetPropertyValue\<T\>()

Gets a typed property value.

```csharp
public static T GetPropertyValue<T>(object target, string propertyName)
```

### SetPropertyValue()

Sets a property value.

```csharp
public static void SetPropertyValue(object target, string propertyName, object value)
```

## Practical Examples

### Inspect Object Structure

```csharp
void InspectObject(object obj)
{
    if (obj == null) return;

    Type type = obj.GetType();
    MelonLogger.Msg($"=== Inspecting: {type.Name} ===");

    // Get all fields
    var fields = type.GetFields(
        BindingFlags.Public | 
        BindingFlags.NonPublic | 
        BindingFlags.Instance
    );

    MelonLogger.Msg($"Fields: {fields.Length}");
    foreach (var field in fields.Take(10))
    {
        var value = ReflectionHelper.GetFieldValue(obj, field.Name);
        MelonLogger.Msg($"- {field.Name}: {value}");
    }
}
```

### Safe Get Field Value with Fallback

```csharp
T SafeGetFieldValue<T>(object target, string fieldName, T defaultValue = default)
{
    try
    {
        var value = ReflectionHelper.GetFieldValue<T>(target, fieldName);
        return value ?? defaultValue;
    }
    catch (Exception ex)
    {
        MelonLogger.Warning($"Failed to get field {fieldName}: {ex.Message}");
        return defaultValue;
    }
}

// Usage
var maxPlayers = SafeGetFieldValue(room, "_maxPlayers", 4);
```

### Method Invocation with Error Handling

```csharp
bool TryInvokeMethod(
    object target, 
    string methodName, 
    out object? result,
    params object[] parameters
)
{
    try
    {
        result = ReflectionHelper.InvokeMethod(target, methodName, parameters);
        return true;
    }
    catch (Exception ex)
    {
        MelonLogger.Error($"Error calling {methodName}: {ex.Message}");
        result = null;
        return false;
    }
}

// Usage
if (TryInvokeMethod(room, "IsPlayable", out var result))
{
    if (result is bool isPlayable)
    {
        MelonLogger.Msg($"Result: {isPlayable}");
    }
}
```

### Create a Wrapper for Private API

```csharp
class InventoryWrapper
{
    private object inventory;

    public InventoryWrapper(object inv)
    {
        inventory = inv;
    }

    public List<InventoryItem> GetItems()
    {
        return ReflectionHelper.GetFieldValue<List<InventoryItem>>(
            inventory, 
            "SlotItems"
        ) ?? new List<InventoryItem>();
    }

    public int GetMaxSlots()
    {
        return ReflectionHelper.GetFieldValue<int>(inventory, "maxSlots");
    }

    public bool AddItem(InventoryItem item)
    {
        var result = ReflectionHelper.InvokeMethod(inventory, "AddItem", item);
        return result is bool success && success;
    }
}

// Usage
var player = PlayerAPI.GetLocalPlayer();
var inventoryObj = ReflectionHelper.GetFieldValue(player, "inventory");
var inventory = new InventoryWrapper(inventoryObj);

var items = inventory.GetItems();
int maxSlots = inventory.GetMaxSlots();
```

### Cache FieldInfo for Performance

```csharp
class CachedReflection
{
    private static Dictionary<string, FieldInfo> fieldCache = new Dictionary<string, FieldInfo>();

    public static object? GetCachedFieldValue(object target, string fieldName)
    {
        string key = $"{target.GetType().FullName}.{fieldName}";

        if (!fieldCache.TryGetValue(key, out var fieldInfo))
        {
            fieldInfo = target.GetType().GetField(
                fieldName,
                BindingFlags.NonPublic | BindingFlags.Instance | BindingFlags.Public
            );
            
            if (fieldInfo != null)
            {
                fieldCache[key] = fieldInfo;
            }
        }

        return fieldInfo?.GetValue(target);
    }

    public static void ClearCache()
    {
        fieldCache.Clear();
    }
}
```

## Notes

:::warning Important
- Reflection is slower than direct access
- Field and method names may change in game updates
- Always handle exceptions when working with reflection
:::

:::danger Security
- Don't change critical game fields without understanding consequences
- Some changes may lead to crashes or break game logic
- Use changes only for local testing or with player consent
:::

:::tip Tips
- Cache FieldInfo and MethodInfo for performance
- Use try-catch blocks for error handling
- Create wrappers for convenient private API access
- Document used private members for future updates
:::

## Performance

Performance comparison:

| Operation | Direct Access | Reflection | Cached Reflection |
|----------|--------------|------------|-------------------|
| Field read | 1x | ~100x | ~10x |
| Method call | 1x | ~50x | ~5x |

**Recommendations:**
- Cache FieldInfo/MethodInfo for frequent access
- Avoid reflection in Update() without caching
- Use reflection only when no alternatives exist

## Alternatives

Instead of ReflectionHelper consider:
- **Harmony** - for method patching
- **Public APIs** - if available
- **Events** - for subscribing to events

## See Also

- [CoreAPI](./core.md) - ReflectionHelper usage examples
- [RoomAPI](./room.md) - Private field access examples
- [ServerNetworkAPI](./server-network.md) - Advanced reflection work

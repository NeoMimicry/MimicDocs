---
sidebar_position: 8
---

# ReflectionHelper

ReflectionHelper is the low-level utility that all other APIs use internally. You only need it directly when you want to access game data that no API exposes yet.

## The auto-property naming rule

C# auto-properties compile to a hidden backing field named `<PropertyName>k__BackingField`. When a game field is an auto-property you must use this exact name:

```csharp
var vworld   = ReflectionHelper.GetFieldValue(hub, "<vworld>k__BackingField");
var currency = ReflectionHelper.GetFieldValue<int>(room, "<Currency>k__BackingField");
```

Plain fields use their name directly:

```csharp
var vrm   = ReflectionHelper.GetFieldValue(vworld, "_vRoomManager");
var pdata = ReflectionHelper.GetFieldValue(hub, "pdata");
```

If you're not sure which pattern a field uses, run Fish AI Reader on `Assembly-CSharp.dll` and look at the field name in the output.

## Field access

### GetFieldValue()

```csharp
public static object? GetFieldValue(object target, string fieldName)
public static object? GetFieldValue(Type type, string fieldName)
```

### GetFieldValue\<T\>()

```csharp
public static T GetFieldValue<T>(object target, string fieldName)
public static T GetFieldValue<T>(Type type, string fieldName)
```

Returns `default` if the field is null or not found — never throws.

```csharp
var room = RoomAPI.GetCurrentRoom();
int day  = ReflectionHelper.GetFieldValue<int>(room, "_currentDay");
long tick = ReflectionHelper.GetFieldValue<long>(room, "_currentTick");
```

### SetFieldValue()

```csharp
public static void SetFieldValue(object target, string fieldName, object value)
public static void SetFieldValue(Type type, string fieldName, object value)
```

```csharp
ReflectionHelper.SetFieldValue(room, "_currentDay", 3);
```

:::warning
Setting internal fields can break game logic. Only do this if you know what the field controls.
:::

## Method invocation

### InvokeMethod()

```csharp
public static object? InvokeMethod(object target, string methodName, params object[] parameters)
public static object? InvokeMethod(Type type, string methodName, params object[] parameters)
```

```csharp
var room = RoomAPI.GetCurrentRoom();

bool playable = ReflectionHelper.InvokeMethod(room, "IsPlayable") is bool b && b;
bool allDead  = ReflectionHelper.InvokeMethod(room, "IsAllPlayerDead") is bool d && d;
```

With parameters:

```csharp
var waitingRoom = ServerNetworkAPI.GetWaitingRoom();
var result = ReflectionHelper.InvokeMethod(waitingRoom, "CanEnterChannel", playerUID);
```

### InvokeMethod\<T\>()

```csharp
public static T? InvokeMethod<T>(object target, string methodName, params object[] parameters) where T : class
```

## Property access

### GetPropertyValue() / GetPropertyValue\<T\>()

```csharp
public static object? GetPropertyValue(object target, string propertyName)
public static T GetPropertyValue<T>(object target, string propertyName)
```

### SetPropertyValue()

```csharp
public static void SetPropertyValue(object target, string propertyName, object value)
```

## Finding field names with Fish AI Reader

When a game update breaks something, run Fish AI Reader to get the current field names:

```bash
FishAIReader.exe Assembly-CSharp.dll -o report.json
```

Then search the JSON for the type you care about and check its `fields` array. The `name` property is exactly what you pass to `GetFieldValue`.

## Full example — inspect a room

```csharp
var room = RoomAPI.GetCurrentRoom();
if (room == null) return;

var type = room.GetType();
MelonLogger.Msg($"Type: {type.Name}");

foreach (var field in type.GetFields(
    System.Reflection.BindingFlags.NonPublic |
    System.Reflection.BindingFlags.Instance  |
    System.Reflection.BindingFlags.Public))
{
    var value = ReflectionHelper.GetFieldValue(room, field.Name);
    MelonLogger.Msg($"  {field.Name} = {value}");
}
```

## See also

- [CoreAPI](./core.md) — shows the full Hub → VWorld chain with real field names
- [RoomAPI](./room.md) — uses reflection for all room field access

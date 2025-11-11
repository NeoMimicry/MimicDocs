# Getting Started

## Requirements

Before you begin, ensure you have:

- **Visual Studio 2019+** or **JetBrains Rider**
- **.NET SDK** (for .NET Standard 2.1)
- **MelonLoader** installed in your Mimesis game folder
- The game **Mimesis**

## Installation

MimicAPI is a modding library and is **not available via NuGet**. It's designed to be used directly in your mod projects.

### Option 1: Download Pre-built DLL (Recommended)

1. Download the latest release of `MimicAPI.dll` from [GitHub Releases](https://github.com/NeoMimicry/MimicAPI/releases)
2. Add the DLL as a reference to your mod project

#### Visual Studio

1. Right-click on **References** → **Add Reference**
2. Click **Browse** and select `MimicAPI.dll`
3. Click **OK**

#### Rider

1. Right-click on project → **Add** → **Add Reference**
2. Select **Add From** → **Browse**
3. Select `MimicAPI.dll`

### Option 2: Build from Source

If you want to modify MimicAPI or build from source:

1. Clone the repository:

```bash
git clone https://github.com/NeoMimicry/MimicAPI.git
```

2. Open `MimicAPI.sln` in Visual Studio or Rider

3. Configure game assembly paths in `Directory.Build.props`:

```xml
<PropertyGroup>
  <GameAssemblyPath>C:\Path\To\Mimesis\Mimesis_Data\Managed</GameAssemblyPath>
  <BepInExAssemblyPath>C:\Path\To\Mimesis\MelonLoader\Managed</BepInExAssemblyPath>
</PropertyGroup>
```

4. Build the project:

```bash
dotnet build
```

5. Add the built DLL to your mod project

## Setting Up Your Mod Project

### 1. Create a New Project

```bash
dotnet new classlib -n MyMimesisMod -f netstandard2.1
```

### 2. Add MimicAPI Reference

Add the reference in your `.csproj` file:

```xml
<ItemGroup>
  <Reference Include="MimicAPI">
    <HintPath>path\to\MimicAPI.dll</HintPath>
  </Reference>
</ItemGroup>
```

### 3. Add Required References

Your mod also needs references to:
- MelonLoader.dll
- Unity and game assemblies
- FishNet.Runtime.dll (if using network features)

### 4. Create Your Mod Class

MimicAPI works with **MelonLoader**, so use MelonMod as your base class:

```csharp
using MelonLoader;
using MimicAPI.GameAPI;
using UnityEngine;

[assembly: MelonInfo(typeof(MyMimesisMod.MyMod), "My Mimesis Mod", "1.0.0", "YourName")]
[assembly: MelonGame("Mimesis", "Mimesis")]

namespace MyMimesisMod
{
    public class MyMod : MelonMod
    {
        public override void OnInitializeMelon()
        {
            LoggerInstance.Msg("Mod loaded!");
        }

        public override void OnUpdate()
        {
            // Get local player
            var player = PlayerAPI.GetLocalPlayer();
            
            if (player != null && Input.GetKeyDown(KeyCode.F1))
            {
                LoggerInstance.Msg($"Player position: {player.transform.position}");
            }
        }
    }
}
```

## Verification

Create a simple test to verify MimicAPI is working:

```csharp
public override void OnInitializeMelon()
{
    // Test CoreAPI
    var hub = CoreAPI.GetHub();
    LoggerInstance.Msg($"Hub initialized: {hub != null}");

    // Note: Player and Room APIs may return null until game is fully loaded
    LoggerInstance.Msg("MimicAPI is ready!");
}
```

## Important Notes

:::warning MelonLoader Only
MimicAPI is designed specifically for **MelonLoader**. It does not support BepInEx or other mod loaders.
:::

:::info No NuGet Package
MimicAPI is **not available on NuGet**. This is intentional as it's a modding library meant to be referenced directly in your mod projects.
:::

## Next Steps

- Learn about [CoreAPI](./api/core.md) for working with core game components
- Learn about [PlayerAPI](./api/player.md) for working with players

## Troubleshooting

### MissingMethodException

If you get `MissingMethodException`:
- Ensure your MimicAPI version matches your game version
- Verify all dependencies are correctly referenced

### NullReferenceException

If API methods return `null`:
- Make sure the game is fully loaded
- Check if you're in an active game session (for RoomAPI, PlayerAPI)
- Always check for `null` before using objects:

```csharp
var player = PlayerAPI.GetLocalPlayer();
if (player != null)
{
    // Safe to use player
}
```

### Game Not Loading

If the game crashes on startup:
- Verify MelonLoader is correctly installed
- Check MelonLoader console for errors
- Ensure all DLL references are correct in your project

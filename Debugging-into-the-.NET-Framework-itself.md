# Debugging into .NET Core (version 2.1 or newer)

Starting with .NET Core 2.1, it is possible to easily debug into the .NET Framework itself.  

### Preparation steps:
1. Make sure you have at least version 2.1 of .NET Core installed on your machine.   Typing 'dotnet --version' will do this.   The lastest version of .NET Core can be downloaded from  https://www.microsoft.com/net/download.  
2. Set TargetFramework to `netcoreapp2.1' (or newer) in your .csproj.
3. Open up your launch.json file. If you aren't familiar with launch.json or debugging .NET Core in VS Code see [here](https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger.md).

### Turning on .NET Framework stepping
Open your launch.json file, and add the following entries to the end of your current configuration. 

**Note:** if you already have an `env` property in your launch.json, move the `COMPlus_*` environment variables to that block rather than creating a second `env` property.

```json
    "justMyCode": false,
    "symbolOptions": {
        "searchMicrosoftSymbolServer": true
    },
    "suppressJITOptimizations": true,
    "env": {
        "COMPlus_ZapDisable": "1",
        "COMPlus_ReadyToRun": "0"
    }
```

When you start debugging, symbols should now download from the internet, and if you stop in .NET Framework code, or click on a stack frame, the debugger should automatically download sources.

After you have debugged and downloaded all the symbols you need, comment out the `"searchMicrosoftSymbolServer": true` so that the debugger doesn't always go to the internet and search for symbols for any dlls which don't have symbols on the Microsoft symbol server.

### Explanation about what the options are doing

> `"justMyCode": false,`

Just My Code is a feature that makes it easier to find problems in your code by ignoring code that is optimized or you don't have symbols for. Technically you can still debug into the .NET Framework with `justMyCode` enabled as the other options effectively tell the debugger to think of the .NET Framework as your code, but that is a confusing way to think about things, so it is better to turn the feature off while you are debugging into the .NET Framework.


> `"searchMicrosoftSymbolServer": true`

This adds the Microsoft Symbol Server (https://msdl.microsoft.com/download/symbols) to the end of the symbol search path. So if a module loads, and the debugger cannot find symbols for it any of the other places, it will then search the Microsoft Symbol Server.

> `"suppressJITOptimizations": true`

This option disables optimizations when .NET Framework assemblies load. See [here](https://aka.ms/VSCode-CS-LaunchJson#suppress-jit-optimizations) for a full explanation.

> `"COMPlus_ZapDisable": "1"` and `"COMPlus_ReadyToRun": "0"`

These environment variables tells the .NET Runtime that it should ignore the ahead-of-time compiled native code that is in many .NET Framework assemblies, and it should instead compile these assemblies to native code just-in-time. The `ZapDisable` version is for the older technology that CLR has for ahead-of-time compilation, and `ReadyToRun` is the newer version. If you are targeting .NET Core 3.0 or newer, there are no 'zaps' anymore, so only the later option matters. Together these are important because `"suppressJITOptimizations": true` doesn't affect assemblies that have already been compiled to native code. So the two options work together to make it so that the .NET Framework runs without optimizations.

## Debugging into other open-source nuget packages

If you would like to debug into other open-source nuget packages, such as Newtonsoft.Json, you can also enable `searchNuGetOrgSymbolServer`. Example:

```json
    "symbolOptions": {
        "searchMicrosoftSymbolServer": true,
        "searchNuGetOrgSymbolServer": true
    }
```

A few notes:
1. Not every library on nuget.org will have their .pdb files indexed. If you find that the debugger cannot find a pdb file for an open source library you are using, please encourage the open source library to upload their PDBs ([see here for instructions](https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages-snupkg)).
2. Most libraries on nuget.org are **not** ahead-of-time compiled, so if you are only trying to debug into this library and not the .NET Framework itself, you can likely omit the `env` section from above. Using an optimized .NET Framework will significantly improve performance in some cases.
3. Only Microsoft provided libraries will have their .pdb files on the Microsoft symbol server, so you can omit `searchMicrosoftSymbolServer` if you are only interested in an OSS library.
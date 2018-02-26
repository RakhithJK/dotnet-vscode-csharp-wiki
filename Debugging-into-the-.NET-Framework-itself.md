# This page is a work in progress. Ignore for now.

# Debugging into .NET Core 2.1

Starting with .NET Core 2.1 Preview 1 and C# Extension version 1.15, it is possible to fairly easily debug into the .NET Framework itself. For preview 1 builds, this is currently only possible for ASP.NET assemblies, but it should work for all of the .NET Framework assemblies before the final build of 2.1 ships.

#### Preparation steps:
1. Install the latest 1.15 beta. [Instructions are here](https://github.com/OmniSharp/omnisharp-vscode/wiki/Installing-Beta-Releases).
2. Install the .NET Core 2.1 Preview 1 SDK (TODO: Add link), and set `TargetFramework` to `netcoreapp2.1' in your .csproj.
3. Open up your launch.json file. If you aren't familiar with launch.json or debugging .NET Core in VS Code see [here](https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger.md).
4. If, in step 2, you changed your app to target netcoreapp2.1, make sure to update the path in the `program` to also point at the `netcoreapp2.1`.

#### Turning on .NET Framework stepping
Open your launch.json file, and add the following entries to the end of your current configuration configuration. 

**Note:** if you already have an `env` entry in your launch.json, move `"COMPlus_ZapDisable": "1"` to that block.

```json
    "justMyCode": false,
    "symbolOptions": {
        "searchMicrosoftSymbolServer": true
    },
    "suppressJITOptimizations": true,
    "env": {
        "COMPlus_ZapDisable": "1"
    }
```

When you start debugging, symbols should now download from the internet, and if you stop in .NET Framework code, or click on a stack frame, the debugger should automatically download sources.

After you have debugged and you have downloaded all the symbols you need, comment out the `"searchMicrosoftSymbolServer": true` so that the debugger doesn't always go to the internet and search for symbols for any dlls which don't have symbols on the Microsoft symbol server.

## What does this stuff actually do?

> `"justMyCode": false,`

Just My Code is a feature that makes it easier to find problems in your code by ignoring code that is optimized or you don't have symbols for. Technically you can still debug into the .NET Framework with `justMyCode` enabled as the other options effectively tell the debugger to think of the .NET Framework as your code, but that is a confusing way to think about things, so it is better to turn the feature off.

> `"searchMicrosoftSymbolServer": true`

TODO

> `"suppressJITOptimizations": true`

TODO

> `"COMPlus_ZapDisable": "1"`

TODO

# Debugging into .NET Core 1.0, 1.1 or 2.0

TODO
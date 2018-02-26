# This page is a work in progress. Ignore for now.

# Debugging into .NET Core 2.1

Starting with .NET Core 2.1 and C# Extension version 1.15, it is possible to fairly easily debug into the .NET Framework itself. For preview 1 builds, this is currently only possible for ASP.NET assemblies, but it should work for all of the .NET Framework assemblies before the final build of 2.1 ships.

Instructions:
1. Install the latest 1.15 beta. [Instructions are here](https://github.com/OmniSharp/omnisharp-vscode/wiki/Installing-Beta-Releases).
2. Open up your launch.json file. If you aren't familiar with launch.json or debugging .NET Core in VS Code see [here](https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger.md).
3. Add the following entries to the end of your current configuration configuration. Note that if you already have an `env` entry in your launch.json, move `"COMPlus_ZapDisable": "1"` to that block so that you have valid json.

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

4. Hit F5 and symbols should download from the internet, and if you stop in .NET Framework code, or click on a stack frame, the debugger should automatically download sources.
5. After you have debugged and you have downloaded all the symbols you need, comment out the `"searchMicrosoftSymbolServer": true` so that the debugger doesn't try to hit the internet on every debugger launch to find any symbol that it couldn't find.

## What does this stuff actually do?

> `"justMyCode": false,`

Just My Code is a feature that attempts to differentiate library code from the code that you are developing to make it easier to focus on the code under your control. Technically you can still debug into the .NET Framework with justMyCode on as all the other options effectively tell the debugger to think of the .NET Framework as your code, but that is a confusing way to think about things. So lets just turn it off.

> `"searchMicrosoftSymbolServer": true`

TODO

> `"suppressJITOptimizations": true`

TODO

> `"COMPlus_ZapDisable": "1"`

TODO

# Debugging into .NET Core 1.0, 1.1 or 2.0

TODO
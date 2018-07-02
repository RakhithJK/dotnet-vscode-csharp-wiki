# Debugging into .NET Core 2.1

Starting with .NET Core 2.1 and C# Extension version 1.15, it is possible to easily debug into the .NET Framework itself.  

### Preparation steps:
1. Confirm you OmniSharp C# VSCode extension 1.15 or later version installed into VSCode by selecting View->Extentions.  If not update your 'C# For Visual Stuiod Code (powered by OmniSharp)' extension.  
2. Make sure you have at least version 2.1 of .NET Core installed on your machine.   Typing 'dotnet --version' will do this.   The lastest version of .NET Core can be downloaded from  https://www.microsoft.com/net/download.  
3. Set TargetFramework to `netcoreapp2.1' in your .csproj.
3. Open up your launch.json file. If you aren't familiar with launch.json or debugging .NET Core in VS Code see [here](https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger.md).
4. If, in step 3, you changed your app to target netcoreapp2.1, make sure to update the path in the `program` to also point at the `netcoreapp2.1`.

### Turning on .NET Framework stepping
Open your launch.json file, and add the following entries to the end of your current configuration. 

**Note:** if you already have an `env` property in your launch.json, move `"COMPlus_ZapDisable": "1"` to that block rather than creating a second `env` property.

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

After you have debugged and downloaded all the symbols you need, comment out the `"searchMicrosoftSymbolServer": true` so that the debugger doesn't always go to the internet and search for symbols for any dlls which don't have symbols on the Microsoft symbol server.

### Explanation about what the options are doing

> `"justMyCode": false,`

Just My Code is a feature that makes it easier to find problems in your code by ignoring code that is optimized or you don't have symbols for. Technically you can still debug into the .NET Framework with `justMyCode` enabled as the other options effectively tell the debugger to think of the .NET Framework as your code, but that is a confusing way to think about things, so it is better to turn the feature off while you are debugging into the .NET Framework.


> `"searchMicrosoftSymbolServer": true`

This adds the Microsoft Symbol Server (https://msdl.microsoft.com/download/symbols) to the end of the symbol search path. So if a module loads, and the debugger cannot find symbols for it any of the other places, it will then search the Microsoft Symbol Server.

> `"suppressJITOptimizations": true`

This option disables optimizations when .NET Framework assemblies load. See [here](https://aka.ms/VSCode-CS-LaunchJson#suppress-jit-optimizations) for a full explanation.

> `"COMPlus_ZapDisable": "1"`

This environment variable tells the .NET Runtime that it should ignore the ahead-of-time compiled native code that is in many .NET Framework assemblies, and it should instead compile these assemblies to native code just-in-time. This is important because `"suppressJITOptimizations": true` doesn't affect assemblies that have already been compiled to native code. So the two options work together to make it so that the .NET Framework runs without optimizations.

# Debugging into .NET Core 1.0, 1.1 or 2.0

The versions of .NET Core before 2.1 didn't uniformly publish their Portable PDBs on the Microsoft Symbol Server, and they weren't compiled with [Source Link](https://aka.ms/SourceLinkSpec).   Some (2.0.X version were updated by hand, so you can try the procedure above on apps using those version of the runtime and they might work, but the guidance is to upgrade to 2.1 (it should be painless) 

If you can't upgrade the runtime, and simply trying it did not work, then it is still possible to debug through the framework but you need to basically build it yourself (so you have PDBs and the source code locally).   (Obviously this is not nearly as convenient, but it you can do it in less than one hour).  

Here is what you would need to do:

1. Clone the [repo](https://github.com/dotnet) for whatever .NET Framework assembly you want to debug into.
2. Create a branch at the release tag for whatever release you have
3. Build this repo
4. Change your application so that it has its own copy of the .NET Framework by making it a 'Self-Contained Deployment'. There are instructions on [docs.microsoft.com](https://docs.microsoft.com/en-us/dotnet/core/deploying/deploy-with-cli#simpleSelf) for this. In brief you need to set a `RuntimeIdentifiers` in your .csproj, publish your app, and change your launch.json to run your app out of this publishing directory. You also would want to either disable the `preLaunchTask`.
5. Replace the .NET Framework assembly that you want to debug into with the one you just built.
6. Debug
6. If you need to make changes to your code, be sure to build and publish your app again, and then again replace the .NET Framework assembly.

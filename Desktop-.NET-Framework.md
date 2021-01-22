The C# extension supports limited full .NET framework debugging. It can only debug 64-bit applications with [portable PDBs](https://github.com/OmniSharp/omnisharp-vscode/wiki/Portable-PDBs).

To enable the Desktop CLR debugger, change the configuration type in launch.json to be "clr" instead of "coreclr" and program should be pointing at the exe (**NOT** a .dll).

For unit tests, this can be done thusly:
1. File->Preferences->Settings
2. Open "CSharp: Unit Test Debugging Options"
3. Set the 'type' to 'clr' (see settings.json example below)

## launch.json example

```
{
   ...
   "type": "clr",
   "program": "path\\to\\program.exe",
   ...
}
```

More information about debugging desktop .NET Framework can be found here, https://stackoverflow.com/questions/47707095.


## settings.json example

```
{
    ...
    "csharp.unitTestDebuggingOptions": {
        "type": "clr"
    }
}
```
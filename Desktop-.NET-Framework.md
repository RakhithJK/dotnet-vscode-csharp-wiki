The C# extension supports limited full .NET framework debugging. It can only debug 64-bit applications with [portable PDBs](https://github.com/OmniSharp/omnisharp-vscode/wiki/Portable-PDBs).

To enable the Desktop CLR debugger, change the configuration type in launch.json to be "clr" instead of "coreclr" and program should be pointing at the exe.

## launch.json example

```
{
   ...
   "type": "clr",
   "program": "path\\to\\program.exe",
   ...
}
```
Here is how to enable additional logging for the VS Code C# debugger to help troubleshoot problems.

## Quick Method
The quick way to enable logging is to modify your .vscode/launch.json file and add a new 'logging' entry into your active configuration. This works in all cases except problems that happen very early in the initialization stage of the debugger. Here is an example of the new sections to add to launch.json:

```json
"configurations": [
    {
        "...": "...",
        "logging": {
            "engineLogging": true
        }
    },
    { "...": "..." }
]
```

When this is enabled, logging will be sent to the VS Code Debug Console where you can copy/paste the relevant sections.

## Full method
If you are dealing with a problem that happens either very early on during debugger startup, or a problem where the debugger is crashing, it can be helpful to run the debugger (vsdbg-ui) in the console.

To do this:

1. Open up a terminal (command prompt) window
2. Change to the directory of the debugger. (NOTE: if you are using VS Code Insiders, change `.vscode` to `.vscode-insiders`)
    * **Linux/macOS**: `cd ~/.vscode/extensions/ms-vscode.csharp-<insert-version-here>/.debugger`
    * **Windows**: `cd /d C:\Users\<your-username>\.vscode\extensions\ms-vscode.csharp-<insert-version-here>\.debugger`
3. Run vsdbg-ui: `./vsdbg-ui --server --consoleLogging`
4. Go back to VS Code and open your `.vscode\launch.json` file.
5. Go to the section for of launch.json for your current launch configuration and add: `"debugServer": 4711`
6. Debug as normal
7. When the problem happens, look at what is printed into the terminal.

Example launch.json configuration:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "debugServer": 4711,
            "name": ".NET Core Launch (console)",
            "...": "...",
        },
        { "...": "..." }
    ]
}
```

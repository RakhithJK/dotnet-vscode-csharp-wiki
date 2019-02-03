Here is how to enable additional logging for the VS Code C# debugger to help troubleshoot problems.

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

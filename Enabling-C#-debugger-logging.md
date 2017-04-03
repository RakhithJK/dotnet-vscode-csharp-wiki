Here is how to enable additional logging for the VS Code C# debugger to help troubleshoot problems.

## Quick method
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
If you are dealing with a problem that happens either very early on during debugger startup, or you want to gather complete logs, it is also possible to send logging output to a file. Though this method requires a bit more work. Here are the steps.

#### 1: Open package.json
Fire up your favorite editor and open the C# extension's package.json file. You might want to use something other than VS Code so that you can restart VS Code without having to close this file, but it isn't required. The package.json can be found here:

* **Windows:** C:\Users\\\<your-username\>\\.vscode\\extensions\\ms-vscode.csharp-\<ver\>\\package.json
* **OSX/Linux:** ~/.vscode/extensions/ms-vscode.csharp-\<ver\>/package.json

**NOTE:** If you are using the Insiders build of VS Code, replace '.vscode' with '.vscode-insiders'.

#### 2: Find the debug adapter's 'program' property
We want to find the 'contributes.debuggers[0].\<os-name\>.program' element in package.json. The fastest way to do this is to search for '.debugger/vsdbg-ui'. You should see something like this:

```json
        "windows": {
          "program": "./.debugger/vsdbg-ui.exe"
        },
        "osx": {
          "program": "./.debugger/vsdbg-ui"
        },
        "linux": {
          "program": "./.debugger/vsdbg-ui"
        }
```

#### 3: Add 'args' property
Add a new property after program to pass command line arguments to vsdbg-ui. We want to pass '--engineLogging' which will save a log to $HOME/vsdbg-ui.log (or %HOMEDRIVE%%HOMEPATH%\vsdbg-ui.log on Windows).

```json
  "osx": {
    "program": "./debugger/vsdbg-ui",
    "args": [ "--engineLogging" ]
  }
```

If you wish to specify where to save the log file, you can use the following schema:

```json
  "osx": {
    "program": "./debugger/vsdbg-ui",
    "args": [ "--engineLogging=<logfile>" ]
  }
```

#### 4: Debug

Restart VS Code to make sure that VS Code re-reads package.json. Then start debugging as normal. Debug until the problem is hit, then stop debugging. You should now see that the log file has been written.

#### 5: Restore package.json

Put back package.json as it was before (no logging).

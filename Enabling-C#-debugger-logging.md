Here is how to enable additional logging for the VS Code C# debugger to help troubleshoot problems.

#### 1: Open package.json
Fire up your favorite editor and open the C# extension's package.json file. You might want to use something other than VS Code so that you can restart VS Code without having to close this file, but it isn't required. Package.json can be found here:

* **Windows:** C:\Users\\\<your-alias\>\\.vscode\extensions\ms-vscode.csharp-\<ver\>\\package.json
* **OSX/Linux:** ~/.vscode/extensions/ms-vscode.csharp-\<ver\>/package.json

**NOTE:** If you are using the Insiders build of VS Code, replace '.vscode' with '.vscode-insiders'.

#### 2: Find the debug adapter's 'program' property
We want to find the 'contributes.debuggers[0].program' element in package.json. The fastest way to do this is to search for 'OpenDebugAD7'. You should see something like this:

        ...
        "program": "./coreclr-debug/debugAdapters/OpenDebugAD7.exe",
        ...

#### 3: Add 'args' property
Add a new property after program to pass command line arguments to OpenDebugAD7. We want to pass '--engineLogging=<path-to-file>'. This example logs to c:\\users\\greggm\\cs-debug.log. Replace this with a path that is easy for you.

        ...
        "args": [ "--engineLogging=c:\\users\\greggm\\cs-debug.log" ],
        ...

#### 4: Debug

Restart VS Code to make sure that VS Code re-reads package.json. Then start debugging as normal. Debug until the problem is hit, then stop debugging. You should now see that the log file has been written.

#### 5: Restore package.json

Put back package.json as it was before (no logging).

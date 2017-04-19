With the Windows 10 Creators Update (Windows version 10.0.15063), you can use Visual Studio Code to debug .NET core applications on [Windows Subsystem for Linux (WSL)](https://msdn.microsoft.com/en-us/commandline/wsl/about).

This page will walk you through the steps required to debug a .NET core application on WSL.

## Prerequisites
* [Windows 10 Creators Update with Windows Subsystem for Linux](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) and Bash installed.
* .NET Core on WSL
* Visual Studio Code 
* Microsoft C# extension for VSCode. 

Be sure to check the version of Ubuntu on WSL. The Windows 10 Creators Update comes with 16.04.2 LTS version of Ubuntu. You can confirm that by running the command below.

```
~$ cat /etc/os-release  | grep  -i version
VERSION="16.04.2 LTS (Xenial Xerus)"
VERSION_ID="16.04"
VERSION_CODENAME=xenial
```

If the version is 14, run the following commands in a cmd to reinstall and update WSL. 

_Note: These commands remove everything in your current WSL. Please be sure to save any files you wish to keep._

```
lxrun /uninstall /full
lxrun /install
```

Go to [https://www.microsoft.com/net/core#linuxubuntu](https://www.microsoft.com/net/core#linuxubuntu) follow the instructions to install .NET core in WSL. You will need to follow the 16.04 instructions.

## Install the debugger
You can download a copy of the debugger with:

```
sudo apt-get install unzip
curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l ~/vsdbg
```

This will download and install the debugger at `~/vsdbg/vsdbg`. This will be used later as the `debuggerPath`.

## Sample launch configuration

```json
        {
           "name": ".NET Core WSL Launch",
           "type": "coreclr",
           "request": "launch",
           "preLaunchTask": "build",
           "program": "/mnt/c/temp/dotnetapps/wslApp/bin/Debug/netcoreapp1.1/wslApp.dll",
           "args": [],
           "cwd": "/mnt/c/temp/dotnetapps/wslApp",
           "stopAtEntry": false,
           "console": "internalConsole",
           "pipeTransport": {
               "pipeCwd": "${workspaceRoot}",
               "pipeProgram": "bash.exe",
               "pipeArgs": [ "-c" ],
               "debuggerPath": "~/vsdbg/vsdbg"
           }
       }
```

The sample application shown here was created in the Windows path `C:\temp\dotnetapps\wslApp`. WSL by default allows windows paths to be accessible through `/mnt/<driveletter>/<path>`, so the path above is accessible as `/mnt/c/temp/dotnetapps/wslApp` from WSL. 

Note:
1. `preLaunchTask` executes ```dotnet build```, which builds the project on Windows. Since coreclr is cross-platform, the binary can be executed on WSL without any extra work.
2. `pipeProgram` is set to bash.exe. 
3. `debuggerPath` points to vsdbg, the coreclr debugger.

## Sample attach configuration

```json
       {
           "name": ".NET Core WSL Attach",
           "type": "coreclr",
           "request": "attach",
           "processId": "${command:pickRemoteProcess}",
           "pipeTransport": {
               "pipeCwd": "${workspaceRoot}",
               "pipeProgram": "bash.exe",
               "pipeArgs": [ "-c" ],
               "debuggerPath": "~/vsdbg/vsdbg",
               "quoteArgs": true
           }
       }
```
1. `"processId": "${command:pickRemoteProcess}"` lists the processes running on WSL using the pipe program. 
2. `quoteArgs` will quote any arguments and debugger commands with spaces if set to `true`. 

Note: 
* Use `sourceFileMap` to map sources if they are available in a different location than where they were built. 
* File and paths are case sensitive in Linux.

## Also see
[Configuring C# Launch.json](https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger-launchjson.md)

[C++ debugging in WSL with VSCode C++ Extensions.](https://github.com/Microsoft/vscode-cpptools/blob/master/Documentation/Debugger/gdb/Windows%20Subsystem%20for%20Linux.md)
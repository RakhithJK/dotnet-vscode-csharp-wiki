With Windows 10 Creators Update, you can use Visual Studio Code to debug dotnet core applications on your [Windows Subsystem for Linux (WSL)](https://msdn.microsoft.com/en-us/commandline/wsl/about).

This wiki walks through the steps required to debug dotnet core application on WSL.

## Prerequisites
Windows 10 Creators Update with Windows Subsystem for Linux and Bash installed.
Visual Studio Code and Microsoft C# extension for VSCode. 

Windows 10 Creators update comes with 16.04.2 LTS version of Ubuntu. You can confirm that by running the command below.

```
~$ cat /etc/os-release  | grep  -i version
VERSION="16.04.2 LTS (Xenial Xerus)"
VERSION_ID="16.04"
VERSION_CODENAME=xenial
```

If the version of the installed Ubuntu is 14, use the following commands to install the new WSL.
```
lxrun /uninstall /full
lxrun /install
```

Goto [https://www.microsoft.com/net/core#linuxubuntu](https://www.microsoft.com/net/core#linuxubuntu) follow the instructions to install dotnet core in WSL.

## Install the debugger

```
sudo apt-get install unzip
curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l ~/vsdbg
```

Now the debugger is available at location `~/vsdbg/vsdbg`

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

The sample application shown here lives in the Windows path `C:\temp\dotnetapps\wslApp`, built and runnable in Windows. WSL by default allows windows paths to be accessible through `/mnt/<driveletter>/<path>`, so the path above is accessible as `/mnt/c/temp/dotnetapps/wslApp` from WSL.

Note:
1. `preLaunchTask` executes build, which builds the project on Windows. Since coreclr is cross-platform, the binary can be executed on WSL without any extra work.
2. `pipeProgram` is set to bash.exe. 
3. `debuggerPath` points to vsdbg, the coreclr debugger.

## Sample attach configuration
Note: 
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
2. `quoteArgs` set to `true`. Arguments passed in the `pipeArgs` along with the debugger command will be quoted if `quoteArgs` is set to `true`. 

Note: Use `sourceFileMap` to map sources if they are available in a different location than where they were built. 

## Also see
[C++ debugging in WSL with VSCode C++ Extensions.](https://github.com/Microsoft/vscode-cpptools/blob/master/Documentation/Debugger/gdb/Windows%20Subsystem%20for%20Linux.md)
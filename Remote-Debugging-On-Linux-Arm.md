***This is a beta release feature***

The extension supports remote debugging netcoreapp 2.1 on `linux-arm`. Debugging does not support netcoreapp 2.0. The extension has been tested against **`Raspbian 8 and 9`**. Please let us know if you run into issues with other distributions.

If you run into any problems, please file an [issue](https://github.com/omnisharp/omnisharp-vscode) and note in the text that this is related to `linux-arm`. 

Choose **one** of the following deployment methods:

* [Framework Dependent Deployment](#framework-dependent-deployment): Compile the application locally. Deploy the binary to `linux-arm`. **Requires the .NET Core Runtime to be installed on `linux-arm`.** 

* [Self Contained Deployment](#self-contained-deployment): Compile and publish the application locally. Deploy the standalone application to `linux-arm`.

# Prerequisites

## Install .NET Core 2.1 SDK locally (IDE computer)
* The Preview 1 SDK can be found [here](https://www.microsoft.com/net/download/dotnet-core/sdk-2.1.300-preview1). You can also use [daily builds](https://github.com/dotnet/cli#installers-and-binaries) if you need to be even newer.
* Ensure that .NET Command Line Tools v2.1 or higher is installed
    * Run `dotnet --info` to verify.

        *Example:*
        ```bash
         $ dotnet --info
        .NET Command Line Tools (2.1.300-preview1-008174)
        
        Product Information:
        Version:            2.1.300-preview1-008174
        ...
        ```

## Install the debugger for `linux-arm` (target computer)
* Install the [native dependencies of .NET Core](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x). On Raspbian, the only thing missing is libunwind8.
* Run the following command on `linux-arm` *(installs to ~/vsdbg)*:
```
curl -sSL https://aka.ms/getvsdbgshbeta | bash /dev/stdin -r linux-arm -v latest -l ~/vsdbg
```

# Framework-Dependent Deployment
Framework-dependent deployments are when the application is deployed without a copy of .NET Core itself, so the application depends on the shared .NET Core Framework being installed. See [docs.microsoft.com](https://docs.microsoft.com/en-us/dotnet/core/deploying/) for more information.

## Install prerequisites
* [General prerequisites](#prerequisites)
* On the target computer, install a `linux-arm` build of the .NET Core runtime. The preview1 runtime is at https://dotnetcli.blob.core.windows.net/dotnet/Runtime/2.1.0-preview1-26216-03/dotnet-runtime-2.1.0-preview1-26216-03-linux-arm.tar.gz.

    *Example (installs to ~/dotnet):*
    ```
    mkdir ~/dotnet & curl -sSL https://dotnetcli.blob.core.windows.net/dotnet/Runtime/2.1.0-preview1-26216-03/dotnet-runtime-2.1.0-preview1-26216-03-linux-arm.tar.gz | 
    tar xvzf /dev/stdin -C ~/dotnet
    ```
NOTE: To use a daily build, use https://dotnetcli.blob.core.windows.net/dotnet/Runtime/master/dotnet-runtime-latest-linux-arm.tar.gz instead.

## Create a new console project
On the IDE computer:
* Run `dotnet new console -n MyConsoleApp`. This will create a new netcoreapp v2.1 console application called `MyConsoleApp`.
    * [The restoring of the NuGet packages may fail with an error](https://github.com/dotnet/cli#installers-and-binaries). To download the NuGet packages that match the daily build, you can choose **either** option below:
        * Create a `nuget.config` file with the following block:
        ``` xml
        <configuration>
			  <packageSources>
			    <add key="dotnet-myget" value="https://dotnet.myget.org/F/dotnet-core/api/v3/index.json" />
			  </packageSources>
        </configuration> 
        ```
        * Run `dotnet restore --source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json`. 

## Build and Deploy
On the IDE computer:
* In your application's root folder, run `dotnet build`
* Copy all the files under `bin/Debug/netcoreapp2.1/` to your `linux-arm` device.
    * To test run your application, on `linux-arm`, run the entrypoint `MyConsoleApp.dll` with `dotnet`.
        ```bash
        $ ~/dotnet/dotnet MyConsoleApp.dll
        ```

## Remotely debug your application
Reference the sample `launch.json` below. 
* The `"program"` field is set to the `dotnet` executable and the first `"args"` item is the  application `.dll` relative to the current working directory (`"cwd"`) on `linux-arm`.
* Update the fields under `"pipeArgs"` to include the IP address of the `linux-arm` device and the ssh keyfile. 
* The `"debuggerPath"` points to the location where you installed the debugger to on `linux-arm`.

### Sample `launch.json` - macOS and Linux
```json
    {
        "name": ".NET Core Remote Launch - Framework Dependent (console)",
        "type": "coreclr",
        "request": "launch",
        "program": "~/dotnet/dotnet",
        "args": [
            "./MyConsoleApp.dll"
        ],
        "cwd": "~/MyConsoleApp",
        "stopAtEntry": false,
        "console": "internalConsole",
        "pipeTransport": {
            "pipeCwd": "${workspaceRoot}",
            "pipeProgram": "/usr/bin/ssh",
            "pipeArgs": [
                "-T", "-i", "mysshkeyfile",
                "pi@10.10.10.10"
            ],
            "debuggerPath": "~/vsdbg/vsdbg"
            }
    }
```

### Sample `launch.json` - Windows

This launch.json requires that [PuTTY](http://www.putty.org/) is installed. You must convert your ssh keyfile to a format that PuTTY understands with puttygen. See [How to convert SSH keypairs generated using PuttyGen(Windows) into key-pairs used by ssh-agent and KeyChain(Linux)](https://stackoverflow.com/questions/2224066/how-to-convert-ssh-keypairs-generated-using-puttygenwindows-into-key-pairs-use) for tips.

``` json
 {
            "name": ".NET Core Remote Launch - Framework Dependent (console)",
            "type": "coreclr",
            "request": "launch",
            "program": "~/dotnet/dotnet",
            "args": ["./dotnetapp.dll"],
            "cwd": "~/dotnet-core-app",
            "stopAtEntry": false,
            "console": "internalConsole",
            "pipeTransport": {
                "pipeCwd": "${workspaceRoot}",
                "pipeProgram": "c:\\Program Files\\PuTTY\\plink.exe",
                "pipeArgs": [
                    "-i",
                    "mysshkeyfile.ppk",
                    "pi@10.10.10.10"
                ],
                "debuggerPath": "~/vsdbg/vsdbg"
            }
        }
```

It is likely that other tools than PuTTY can be used. They have not been tested.

We are researching if WSL (ssh) can be used to avoid needing to install PuTTY (or similar tool).

# Self-Contained Deployment
Self-contained deployments are when all of an applications' dependencies are carried with the deployment. So the only thing that must be installed on the target computer is the [native dependencies of .NET Core](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x). See [docs.microsoft.com](https://docs.microsoft.com/en-us/dotnet/core/deploying/) for more information.

## Install prerequisites
* [General prerequisites](#prerequisites)

## Create a new console project
On the IDE computer:
* Run `dotnet new console -n MyConsoleApp`. This will create a new netcoreapp v2.1 console application called `MyConsoleApp`.
    * [The NuGet package restoral may fail to get the necessary packages](https://github.com/dotnet/cli#installers-and-binaries). To download the NuGet packages that match the daily build, you can choose **either** option below:
        * Create a `nuget.config` file with the following block:
        ``` xml
        <configuration>
			  <packageSources>
			    <add key="dotnet-myget" value="https://dotnet.myget.org/F/dotnet-core/api/v3/index.json" />
			  </packageSources>
        </configuration> 
        ```
        * Run `dotnet restore --source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json`. 

## Build and Deploy
On the IDE computer:
* Run `dotnet publish -r linux-arm`
* Copy all the files under `bin/Debug/netcoreapp2.1/linux-arm/publish/` to `linux-arm`.
* Test your application by running the standalone executable `MyConsoleApp`.
    ```bash
    $ ./MyConsoleApp
    ```

## Remotely debug your standalone application executable
Reference the sample `launch.json` below.
* The `"program"` field is the standalone executable relative to the current working directory (`"cwd"`) on `linux-arm`.
* Update the fields under `"pipeArgs"` to include the IP address of the `linux-arm` device and the ssh keyfile. 
* The `"debuggerPath"` points to the location where you installed the debugger to on `linux-arm`.

### Sample `launch.json` -- macOS and Linux
```json
    {
        "name": ".NET Core Remote Launch - Standalone Application (console)",
        "type": "coreclr",
        "request": "launch",
        "program": "MyConsoleApp",
        "args": [],
        "cwd": "~/MyConsoleApp",
        "stopAtEntry": false,
        "console": "internalConsole",
        "pipeTransport": {
            "pipeCwd": "${workspaceRoot}",
            "pipeProgram": "/usr/bin/ssh",
            "pipeArgs": [
                "-T", "-i", "mysshkeyfile",
                "pi@10.10.10.10"
            ],
            "debuggerPath": "~/vsdbg/vsdbg"
            }
    }
```

### Sample `launch.json` - Windows

See Framework Dependent sample above for the appropriate `pipeTransport` section for Windows.
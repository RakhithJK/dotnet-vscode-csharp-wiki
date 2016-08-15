Linux distribution support is a little tricky for the C# extension as both OmniSharp (the language service) and the debugger need a version of .NET Core to run on top of, and both projects include their own copy of .NET Core for this.

There are four buckets that various Linux distros might fall into --

###1: .NET Core officially supports the distro
In this case the C# extension team should be able to produce a new version of the C# extension fairly soon after the .NET Core team publishes nuget packages which the C# extension can download.

###2: .NET Core and clrdbg from another distro are binary compatible
In this case it is just a matter of tweaking this [switch statement](https://github.com/OmniSharp/omnisharp-vscode/blob/735f90a299e857fc5de97216f961c4dd16907cac/src/platform.ts#L59) which parses the output from `cat /etc/os-release`. While we cannot commit to testing on these unofficial distros we are happy to accept a PR to enable the distro, and if there are minor bugs in the debugger we are happy to try and fix them as well.

###3: The community has produced a .NET Core install script, and the public clrdbg can run
For these distributions, the [debugger installer class](https://github.com/OmniSharp/omnisharp-vscode/blob/master/src/coreclr-debug/install.ts) can be run as a [gulp task](https://github.com/OmniSharp/omnisharp-vscode/blob/bc2eb6e5ec90f3b2c9e5a45e3aadd47e28539d01/gulpfile.js#L64). So it might be possible for the community to provide an install script which will find the current installed C# extension, use gulp to have the debugger and OmniSharp download the version of the extension from some other Linux distro, and then patch the CoreCLR/FX which they are running on. The C# extension team is happy to accept tweaks to the install code to make this easier.

###4: New clrdbg is needed
Clrdbg is the cross-platform .NET Core debugger backend. It is based on the same debugger as Visual Studio, and so is a closed-sound product. Distributions where the current public version of clrdbg cannot run are difficult to deal with. In general, we would need official support from the .NET Core team in these cases. Though feel free to open an [issue](https://github.com/OmniSharp/omnisharp-vscode/issues) to discuss the problem.
When you install the C# extension for the first time it will try to download its dependencies. It needs to do this because the C# extension uses a number of platform-dependent files. So the extension itself contains the platform-neutral bits, and it will download the platform-specific bits on first use.

### Installing on an occasionally connected computer

If you are trying to install the C# extension on a computer which is sometimes connected to the internet, this is pretty easy to solve: just make sure you have internet connectivity the first time you use the C# extension. After that you can be offline forever.

### Installing on a computer that cannot connect to the internet

If you are trying to install the C# extension on a computer which cannot be connected to the internet, but you have another computer that is connected to the internet, you can create the collection of platform-specific .vsix files which can be installed to VS Code.

Instructions:

1. If possible, find a non-Windows computer for performing these steps. If you only have a Windows machines, there are a few hacks you will need to make (see the 'If packaging on Windows' steps).
2. Clone this repo: `git clone https://github.com/OmniSharp/omnisharp-vscode.git`
3. Install the tools mentioned in this repo's [readme.md](https://github.com/OmniSharp/omnisharp-vscode/#development).
4. Change directories to the root of the repo
5. Sync the repo to the point of the last release. You can run `git tag` to see all the release tags. Then run something like `git checkout -b installme v1.6.2` to create a new branch (called installme) at the point of the last release (v1.6.2 in that example).
6. `npm i`
7. `npm run compile`
8. **If packaging on Windows:** Edit gulpfile.js and comment out [this throw statement](https://github.com/OmniSharp/omnisharp-vscode/blob/b5eebb25936b3d52120c3adfa9d257cf8c0dc004/gulpfile.js#L95).
9. `node node_modules/gulp/bin/gulp.js package:offline`
10. Select the .vsix file which has the correct platform for the target computer. Transfer this file to that machine and install the .vsix. Instructions on installing .vsix files can be found [here](https://github.com/OmniSharp/omnisharp-vscode/wiki/Installing-Beta-Releases). 
11. **If packaging on Windows:** If you packaged on Windows and are installing to another Windows computer, you are all set. Otherwise you need to do something to `chmod +x` all the executable files as this doesn't propagated properly if the packages are produced on Windows. To do this, `cd ~/.vscode/extensions/ms-vscode.csharp-1.6.2` (replace 1.6.2 with the version you installed; if you are using VS Code Insiders, replace .vscode with .vscode-insiders), and then `chmod +x` the necessary files. You can either do this with `chmod +x -R .` to make every file executable, or if you want to try and do this selectively, see the below appendix.

### Appendix: Executable files on Linux as of 1.6.2 (may be useful for the last step)
```
./bin/run
./bin/mono.linux-x86_64
./node_modules/tmp/cleanup.sh
./node_modules/semver/bin/semver
./node_modules/agent-base/node_modules/semver/bin/semver
./node_modules/open/vendor/xdg-open
./node_modules/mkdirp/bin/cmd.js
./.debugger/vsdbg-ui
./.debugger/vsdbg
```
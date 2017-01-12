When you install the C# extension for the first time it will try to download its dependencies. It needs to do this because the C# extension uses a number of platform-dependent files. So the extension itself contains the platform-neutral bits, and it will download the platform-specific bits on first use.

### Installing on an occasionally connected computer

If you are trying to install the C# extension on a computer which is sometimes connected to the internet, this is pretty easy to solve: just make sure you have internet connectivity the first time you use the C# extension. After that you can be offline forever.

### Installing on a computer that cannot connect to the internet

If you are trying to install the C# extension on a computer which cannot be connected to the internet, but you have another computer that is connected to the internet, you can create the collection of platform-specific .vsix files which can be installed to VS Code.

Instructions:
1: Clone this repo: `git clone https://github.com/OmniSharp/omnisharp-vscode.git`
2: Install the tools mentioned in this repo's [readme.md](https://github.com/OmniSharp/omnisharp-vscode/#development).
3: Change directories to the root of the repo
4: `npm i`
5: `npm run compile`
6: `node node_modules/gulp/bin/gulp.js package:offline`
7: Select the .vsix file which has the correct platform for the target computer. Transfer this file to that machine and install the .vsix. Instructions on installing .vsix files can be found [here](https://github.com/OmniSharp/omnisharp-vscode/wiki/Installing-Beta-Releases). 
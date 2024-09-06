# Indaxia Modules & Packages (IMP)

A simple package and module management for apps written on Lua and other dynamic-typed languages.

Brings package management and es6-like Lua modules to your project without copy-paste pain and dependency hell.

![GifMaker_20240907000902283](https://github.com/user-attachments/assets/241f4613-7396-4a49-891d-9e72cac59654)

## Features
IMP consists of a Package Manager and a Module Manager with it's own Lua part of the code.

A new way to work with lua dependencies: [IMP Module Manager](https://github.com/Indaxia/imp-lua-mm):satellite:

### Package Manager Features
- Works with offline directories and online repositories
- Own package config format in JSON
- Install packages with dependencies from Github and Bitbucket
- Install single files directly from Github, Bitbucket or other hosts (allowing them in config)
- File and directory watcher (sources, config, target)
- Dependency version resolution

### Module Manager Features
- Include custom user directories as advanced sources
- Right dependency order in the target file
- ES6-like imports and exports in the Lua script
- Really fast target builder on-the-go (C# watcher)

## Download

[IMP for Windows x64](https://indaxia.com/public/releases/imp/1.0/Install%20IMP%20for%20Windows.exe)

[IMP for Linux x64](https://indaxia.com/public/releases/imp/1.0/Install%20IMP%20for%20Linux%20x64.exe)

## Quick Start

1. Install IMP
2. For game modding (especial Warcraft 3 Reforged follow [special steps](#quick-start-for-modding-warcraft-3--other-games)
3. (for Linux) create a symlink /usr/bin/imp -> (imp directory)/imp
4. Open any terminal window (press Win+R and enter "cmd")
5. enter ```cd <your project directory>```
6. enter ```imp init src build.lua``` where src can be any sources folder name in the project
8. ```imp watch``` and now you are free to write code and build on the go

To initialize your package enter ```imp init build.lua```. 
It will create imp-package.json and .imp directory with the dependencies. If you use git (mercurial/svn/...) add .imp to your ignore file (.gitignore).

To add new dependency enter ```imp install <package> <version>```

### Quick start for modding Warcraft 3 / other games
- create a project folder with the "src" subfolder
- save your map in "map as a directory" mode into this folder
- Open any terminal window (press Win+R and enter "cmd")
- enter ```cd <your project folder>```
- enter ```imp init src war3map.lua```
- install all the deps you need
- ```imp update```
- ```imp watch```

Now create source files in "src" or save your map to build and test it on the go.

#### Example:
```
imp install https://github.com/Indaxia/imp-demo-hello
```
We don't recommend to use "any" version in public projects. Some scammers or stolen accs may update the code and make it malicious. 

#### Specific version example (retrieve from git tag):
```
imp install https://github.com/Indaxia/imp-demo-hello 1.0
```

Use ```imp watch``` to let watcher notify PM and MM if something changed and perform download new packages and/or rebuild modules.
The watcher waits when one of the following changes:
- any directory from config "sources"
- file from config "target"
- config file itself

To get help about module management refer [IMP Module Manager documentation](https://github.com/Indaxia/imp-lua-mm).

## Advanced Usage

### Including files
You can include files directly (Big Integer in the example):
```
imp install https://raw.githubusercontent.com/DeBos99/lua-bigint/master/bigint.lua * file
```

### Clean init
If you don't want to use MM on the client (Lua) side you can disable it by removing it from dependencies or init the project with init-clean:
```
imp init-clean target.lua
```
With this option IMP just includes code of the dependencies without the MM

### Executing a command after building
It's possible execute a terminal command when the building process finishes:
```
  "afterBuild": "echo \"hello world\""
```
It works for root projects only.

## Publishing Packages

If you want to publish your package folow these steps:
1. Create a git repository at Github or Bitbucket
2. Create imp-package.json in the repository root
3. Add the "dependencies" and "sources" parameters. Refer the full config example below.
4. (optional) add git tag to the repository
5. Now this is an IMP package!

Please refer the [imp-demo](https://github.com/Indaxia/imp-demo-hello) structure for better understanding.

## Full config example (imp-package.json) 

```js
{
    "title": "IMP - Demo Package", // (optional) your package or root project title
    "author": "ScorpioT1000 / scorpiot1000@yandex.ru", // (optional) author information
    "license": "MIT", // (optional) source code license
    "dependencies": { // list of packages and files required by your source code
        // github repository from a master branch
        "https://github.com/123/456": "*",
        
        // github repository from the release tagged as "1.1.1"
        "https://github.com/123/456": "1.1.1",
        
        // bitbucket repository from the release tagged as "1.0.0" in an object format
        "https://bitbucket.org/123/789": { "type": "package", "version": "1.0.0" },
        
        // inserts a file directly from the repository
        "https://github.com/123/456/blob/master/somefile.lua": { 
            "type": "file", 
            "topOrder": true  // omit this option or set to false to insert the file after repositories' sources
        }
    },
    // (optional for root project) where your sources are stored. It's important for the package, but can be omitted for root project (it watches "target")
    "sources": [
        "src"
    ],
    // (optional) where to store compiled build. It works for root project only. You can specify different extension for another language
    "target": "build.lua",
    // (optional) execute this command after build, e.g. "echo \"Hello!\""
    "afterBuild": "",
    // (optional) allow more hosts for direct file dependency (allows github.com and bitbucket.org by default). It works for root project only.
    "allowHosts": []
    // (optional) set custom file extensions to use another language (e.g. "*.js")
    "sourceExtensions": "*.lua",
    // (optional) set custom comment format to use another language (e.g. "//")
    "sourceCommentFormat": "--"
}
```

## Restrictions

1. It doesn't support partial version placeholders like ```1.*``` because it doesn't use package registry
2. It performs full re-download on any config requirement change (planned to fix in the future)

### How to build the source code

Execute:
```
dotnet publish -c Release --self-contained --runtime win-x64 /property:Version=VERSION_HERE
dotnet publish -c Release --self-contained --runtime linux-x64 /property:Version=VERSION_HERE
```

Then use Inno Setup for windows and open Setup\win-x64.iss with it to build the setup file.

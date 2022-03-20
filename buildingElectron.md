# Building Mac electron apps (like Electric Drummer) for Linux and Windows

These are my instructions for building electron apps for Mac (like those developed by <a href="https://github.com/scripting">Dave Winer</a>) for Linux and Windows. As with most things, there is "more than one way to do it", but this worked for me.

For now I am only building zip files of the app, not packages that can be properly installed  in Linux and Windows.

I've tested this with the <a href="http://docserver.scripting.com/drummer/electricDrummer.opml">Electric Drummer</a> and <a href="http://this.how/publicFolder/">Public Folde</a>r apps. I work on a Mac with MacOS 12.3 (Monterey) and Node.js 16 (v16.14.0).  

### electron-builder

There are several alternative tools for building electron apps (including <a href="https://github.com/electron/electron-packager">electron-packager</a>, <a href="https://www.electronforge.io/">electron-forge</a>, <a href="https://www.electron.build/">electron-builder</a>). I found electron-builder to be the easiest and have the most options for building multi-platform apps.


Install the latest electron-builder globally with `npm install -g electron-builder`


### Configuration

electron-builder can be <a href="https://www.electron.build/configuration/configuration">configured</a> within package.json (in a 'build' key) or a separate file. I chose to use an electron-builder.json file.

The existing Public Folder app was <a href="https://github.com/scripting/publicfolder/blob/master/electron/source.opml">built</a> with `sudo electron-packager . "Public Folder" --platform=darwin --arch=all --overwrite --icon=bowlingBall.icns --electron-version=7.1.10`, so I incorporated these settings into my  electron-builder.json

My minimal electron-builder.json (for Public Folder)

```
{
  "electronVersion": "7.1.10",
  "directories": {
    "buildResources": "."
  },
  "mac": {
    "target": "zip",
    "identity": null,
    "icon": "bowlingBall.icns"
  },
  "win": {
    "target": "zip",
    "icon": "bowlingBall.png"
  },
  "linux": {
    "target": "zip"
  }
}
```

### Comments on the configuration

- `directories`: where electron-buiders expects to find the icons. They are in the root directory, so we specify '.' .

- `identity: null`: prevents the app from being signed with an existing Apple Developer ID, you may not need this. I did. 

- If you only have an icon in .icns format, Windows will need an icon as .png or .ico.

- electron-packager requires 'name', 'version', 'author' and 'description' keys to be present in package.json.

### Build command

`electron-builder -mwl ` (short for `electron-builder --mac --windows --linux`). The zip files are saved in a 'dist' directory. For Public Folder I got: 

- `electronPublicFolder-0.4.6-mac.zip`

- `electronPublicFolder-0.4.6-win.zip`

- `electronPublicFolder-0.4.6.zip` (for Linux)


To run on Linux, copy over the zip, extract, with the terminal cd into the created 'electronPublicFolder-0.4.6' folder, then run with `./electronpublicfolder`

To run on Windows, copy over the zip, extract, with the file manager move into the created 'electronPublicFolder-0.4.6' folder, then double-click 'electronPublicFolder.exe' to run.

### Possible future steps

- Build proper packages to install on Windows and Linux, configure by adding or changing the 'target' parameters under <a href="https://www.electron.build/configuration/win">win</a> and <a href="https://www.electron.build/configuration/linux">linux</a> 

- Configure <a href="https://www.electron.build/configuration/publish.html">publishing</a> (for example to S3 or GitHub)

- Configure building packages remotely so building does not depend on the local machine (for example with GitHub Actions) 

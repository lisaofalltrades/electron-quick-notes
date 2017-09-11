# Desktop Apps w/ Electron
## Contents
- [Getting Started](#getting)
- [A Simple App](#simple)
- [Dev Tools](#dev)

## Getting Started
- Intro: [Essential Electron](http://jlord.us/essential-electron/) (read)
- Installation Guide:
- Quick Start: [Getting Started w/ Electron](https://youtu.be/jKzBJAowmGg) (video)

## A simple App
Navigate to desired directory and create an empty folder. For example:

```bash
$ mkdir myApp
```

CD to myApp and run

```bash
$ npm init
```
This will create a package.json file. Go through the prompts to save your dependencies. Next run

```bash
$ npm install electron-prebuilt
```
This will build an electron boiler plate so we can get up and running quickly.

In terminal, create a file called MAIN.JS

```bash
$ touch MAIN.JS
```
- this is the main entry point to the app
- this file will run processes in the background and is in charge of toggling windows

In MAIN.JS, require electron and two core classes. Next, create a window. Yes, it's that easy!

```javascript
// require electron
const electron = require('electron');
// from the electron library, require the app and BrowserWindow functions
const {app, BrowserWindow} = electron;
// when the app is ready, load an empty browser. You can even set width and height!
app.on('ready', () => {
    let win = new BrowserWindow({width:800, height: 600})
    // win is short for window
});
```
The above would just be a blank window. To render something, we will need to use the loadURL function.

```javascript
const electron = require('electron');
const {app, BrowserWindow} = electron;
app.on('ready', () => {
    let win = new BrowserWindow({width:800, height: 600});
    // load the following into the window
    win.loadURL('file://${--direname}/index.html')
    // ${--direname} dictates current directory
});
```

We will now need to create file index.html in your text editor or terminal. Add some basic html tags with text:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Electron App</title>
</head>
<body>
    <h1>Index Page</h1>
</body>
</html>
```
We are now almost ready to run this app. But before we do so, we need to edit our package.json file. Add a start script to easily run our app:

```json
{
  "name": "myApp",
  "version": "1.0.0 ",
  "description": "A minimal Electron application",
  "main": "index.js",
  "scripts": {
    "start": "electron main.js"
},
  "author": "Lisa Nguyen",
  "license": "",
}
```
By doing the above, we only need to run the following command to run MAIN.JS with electron.

```bash
$ npm start
```
This will start our desktop app and you will see index.html displayed in our BrowserWindow object.

## Working with Dev Tools
```javascript
const electron = require('electron');
const {app, BrowserWindow} = electron;
app.on('ready', () => {
    let win = new BrowserWindow({width:800, height: 600});
    win.loadURL('file://${--direname}/index.html')
    win.webContents.openDevTools();
    // electron will open a window with dev inspection tools
});
```

In MAIN.JS , you will need to:
    - require electron library
    - set up a BrowserWindow object
    - Load URLs
- render process

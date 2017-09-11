# Desktop Apps w/ Electron
## Contents
- [Getting Started](#getting)
- [Documentation](#documentation)
- [A Simple App](#simple)
- [Dev Tools](#dev)
- Refactoring
- Menu

## Getting Started
- Intro: [Essential Electron](http://jlord.us/essential-electron/) (read)
- Installation Guide:
- Quick Start: [Getting Started w/ Electron](https://youtu.be/jKzBJAowmGg) (video)

## Documentation
- http://electron.atom.io/

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
- this is the main entry point and process to the app
- MAIN.JS runs the life cycle of the app and other processes in the background

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
  "main": "APP.JS",
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
With Electron, you can use development tools for debugging.
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

## Corresponding JavaScript files
It is suggested to create one JavaScript file per HTML file. In correspondence to INDEX.HTML, we're going to create APP.JS and link in INDEX.HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Electron App</title>
</head>
<body>
    <h1>Index Page</h1>
    <script>require('./APP.JS')</script>
</body>
</html>
```
Then in APP.JS, you can write JavaScript functions for the INDEX.HTML page

```javascript
var link = document.createElement('link');
button.textContent = 'New Window';
document.body.appendChild(link);
});
```

## Putting it all together

We're going to use MAIN.JS to control the program so here, we will write a function to open a new window.

```javascript
// MAIN.JS
const electron = require('electron');
const {app, BrowserWindow} = electron;
app.on('ready', () => {
    let win = new BrowserWindow({width:800, height: 600});
    win.loadURL('file://${--direname}/index.html')
    win.webContents.openDevTools();
});

exports.openWindow = () => {
    let win = new BrowserWindow({width: 400, height: 200});
    win.loadURL('file://${--direname}/about.html');
};
```
Switch back to APP.JS (render process) to link

```javascript
//APP.JS
const remote = require('electron').remote;
const main = remote.require('./main.js');
// remote allows APP.JS to have access to everything in MAIN.JS

var link = document.createElement('link');
link.textContent = 'New Window';
link.addEventListener('click', () => {
    main.openWindow();
}, false);

document.body.appendChild(link);

```
Lastly, we need to create the view for ABOUT.HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Electron App</title>
</head>
<body>
    <h1>About Page</h1>
</body>
</html>
```

## Refactoring

## Creating a Menu
```javascript
// APP.JS
// remote allows APP.JS to have access to everything in MAIN.JS
const remote = require('electron').remote;
// ipc allows us to send calls to MAIN.JS
var ipc = require('ipc');
// gives us access to functions in MAIN.JS
const main = remote.require('./main.js');
// require menu library
var Menu = remote.require('menu');
// build menu from Menu class
var menu = Menu.buildFromTemplate([
    {
        label: 'Electron', // this is given but we use it to create submenus
        submenu: [
            {
                label: 'Preferences', // label
                click: function() { // event handler
                    ipc.send('toggle-prefs'); // this will send a message to APP.JS
                }
            }
        ]
    }
])

// set menu above to default for the app
Menu.setApplicationMenu(menu);

var link = document.createElement('link');
link.textContent = 'New Window';
link.addEventListener('click', () => {
    main.openWindow();
}, false);

document.body.appendChild(link);

```
However, we're going to allow MAIN.JS to control all windows.

```javascript
// MAIN.JS
const electron = require('electron');
const {app, BrowserWindow} = electron;
var ipc = require('ipc');

app.on('ready', () => {
    let win = new BrowserWindow({width:800, height: 600});
    win.loadURL('file://${--direname}/index.html')
    win.webContents.openDevTools();

    var preferencesWindow = new BrowserWindow({
        width: 400,
        height: 400,
        show: false
    });
    preferencesWindow.loadUrl('file://' + __direname + '/preferences.html');

    ipc.on('toggle-prefs', function () {
        if (preferencesWindow.isVisible());
            preferencesWindow.hide();
        else
            preferencesWindow.show();
    });
});

exports.openWindow = () => {
    let win = new BrowserWindow({width: 400, height: 200});
    win.loadURL('file://${--direname}/about.html');
};
```
Lastly, we need to create the view for PREFERENCES.HTML and introduce writing electron into the HTML file.

```html
<!-- PREFERENCES.HTML -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Electron App</title>
</head>
<body>
    <h1>Preferences</h1>
    <script>
    var ipc = require('ipc');

    var link = document.createElement('link');
    link.textContent = 'Save & Close';
    link.addEventListener('click', function () {
        ipc.send('toggle-prefs');
    });
    document.body.appendChild(link);
    </script>
</body>
</html>
```

## More Resources

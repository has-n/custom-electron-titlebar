# Custom Electron Titlebar

This project is a typescript library for electron that allows you to configure a fully customizable title bar.
The titlebar is based on the solution from Visual Studio Code and uses
some of their code.

**This library is for electron 10+, it cannot be used on a basic website or with previous versions of electron.**

[![LICENSE](https://img.shields.io/github/license/AlexTorresSk/custom-electron-titlebar.svg)](https://github.com/AlexTorresSk/custom-electron-titlebar/blob/master/LICENSE)

![Preview 1](screenshots/window_1.png)

![Preview 2](screenshots/window_2.png)

![Preview 3](screenshots/window_3.png)

## Install

```
npm i custom-electron-titlebar
```

or use example folder to init basic electron project with this titlebar.

## Usage

#### Step 1
In your **preload** file add:

```js
const customTitlebar = require('custom-electron-titlebar');

window.addEventListener('DOMContentLoaded', () => {
    new customTitlebar.Titlebar({
        backgroundColor: customTitlebar.Color.fromHex('#ECECEC')
    });

    // ...
})
```

or, if you are using _typescript_
```ts
import { Titlebar, Color } from 'custom-electron-titlebar'

window.addEventListener('DOMContentLoaded', () => {
    new Titlebar({
        backgroundColor: Color.fromHex('#ECECEC')
    });
    
    // ...
})
```

The parameter `backgroundColor: Color` is required, this should be `Color` type.
(View [Update Background](#update-background) for more details).

#### Step 2
Update the code that launches browser window

Add the following line to your `main.js` file:

```js
require('@electron/remote/main').initialize()
```

Then configure the browser window like this. We need
nodeIntegration and we need to enable the remote module for
electron 10+. Further, we assume that the :

```js
var mainWindow = new BrowserWindow({
      width: 1000,
      height: 600,
      frame: false,
      webPreferences: {
          nodeIntegration: true,
          enableRemoteModule: true,
          preload: path.join(__dirname, 'preload.js'),
      }
});
```

Then enable the mainWindow to use the `@electron/remote` pacakge:

```js
require("@electron/remote/main").enable(mainWindow.webContents)
```

#### MacOS / Windows

Custom Electron Titlebar is not really useful on MacOS platforms
where it only tries to simulate the normal MacOS window
design to some extent and it is recommended to exclude
the custom titlebar when your application is run on 
a MacOS platform.

To switch it of for MacOS platforms, you should configure
your browser window like so

```js
var mainWindow = new BrowserWindow({
      // ...
      frame: process.platform === 'darwin',
      // ...
});
```

and similiar on the preload:

```js

window.addEventListener('DOMContentLoaded', () => {
    if (process.platform !== 'darwin') {
        const customTitlebar = require('custom-electron-titlebar');
        new customTitlebar.Titlebar({
            backgroundColor: customTitlebar.Color.fromHex('#ECECEC')
        });
    }

    // ...
})
```

## Options

The interface [`TitleBarOptions`] is managed, which has the following configurable options for the title bar. Some parameters are optional.

| Parameter                      | Type             | Description                                                                                                                     | Default                   |
| ------------------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| backgroundColor **(required)** | Color            | The background color of the titlebar.                                                                                           | #444444                   |
| icon                           | string           | The icon shown on the left side of the title bar.                                                                               | null                      |
| iconsTheme                     | Theme            | Style of the icons.                                                                                                             | Themebar.win              |
| shadow                         | boolean          | The shadow of the titlebar.                                                                                                     | false                     |
| drag                           | boolean          | Define whether or not you can drag the window by holding the click on the title bar.                                            | true                      |
| minimizable                    | boolean          | Enables or disables the option to minimize the window by clicking on the corresponding button in the title bar.                 | true                      |
| maximizable                    | boolean          | Enables or disables the option to maximize and un-maximize the window by clicking on the corresponding button in the title bar. | true                      |
| closeable                      | boolean          | Enables or disables the option of the close window by clicking on the corresponding button in the title bar.                    | true                      |
| order                          | string           | Set the order of the elements on the title bar. (`inverted`, `first-buttons`)                                                   | null                      |
| titleHorizontalAlignment       | string           | Set horizontal alignment of the window title. (`left`, `center`, `right`)                                                       | center                    |
| menu                           | Electron.Menu    | The menu to show in the title bar.                                                                                              | Menu.getApplicationMenu() |
| menuPosition                   | string           | The position of menubar on titlebar.                                                                                            | left 					  |
| enableMnemonics                | boolean 		    | Enable the mnemonics on menubar and menu items.																		          | true                      |
| itemBackgroundColor            | Color            | The background color when the mouse is over the item.                                                                           | rgba(0, 0, 0, .14)        |
| hideWhenClickingClose          | boolean          | When the close button is clicked, the window is hidden instead of closed.                                                       | false                     |
| overflow                       | string           | The overflow of the container (`auto`, `visible`, `hidden`)                                                                     | auto                      |
| unfocusEffect                  | boolean          | Enables or disables the blur option in the title bar.                                                                           | false                     |

## Methods

### Update Background

This change the color of titlebar and it's checked whether the color is light or dark, so that the color of the icons adapts to the background of the title bar.

```js
titlebar.updateBackground(new Color(new RGBA(0, 0, 0, .7)));
```

To assign colors you can use the following options: `Color.fromHex()`, `new Color(new RGBA(r, g, b, a))`, `new Color(new HSLA(h, s, l, a))`, `new Color(new HSVA(h, s, v, a))` or `Color.BLUE`, `Color.RED`, etc.

### Update Items Background Color

This method change background color on hover of items of menubar.

```js
titlebar.updateItemBGColor(new Color(new RGBA(0, 0, 0, .7)));
```

To assign colors you can use the following options: `Color.fromHex()`, `new Color(new RGBA(r, g, b, a))`, `new Color(new HSLA(h, s, l, a))`, `new Color(new HSVA(h, s, v, a))` or `Color.BLUE`, `Color.RED`, etc.

### Update Title

This method updated the title of the title bar, If you change the content of the `title` tag, you should call this method for update the title.

```js
document.title = 'My new title';
titlebar.updateTitle();

// Or you can do as follows and avoid writing document.title
titlebar.updateTitle('New Title');
```

if this method is called and the title parameter is added, the title of the document is changed to that of the parameter.

### Update Icon

With this method you can update the icon. This method receives the url of the image _(it is advisable to use transparent image formats)_

```js
titlebar.updateIcon('./images/my-icon.svg');
```

### Update Menu

This method updates or creates the menu, to create the menu use remote.Menu and remote.MenuItem.

```js
const menu = new Menu();
menu.append(new MenuItem({
	label: 'Item 1',
	submenu: [
		{
			label: 'Subitem 1',
			click: () => console.log('Click on subitem 1')
		},
		{
			type: 'separator'
		}
	]
}));

menu.append(new MenuItem({
	label: 'Item 2',
	submenu: [
		{
			label: 'Subitem checkbox',
			type: 'checkbox',
			checked: true
		},
		{
			type: 'separator'
		},
		{
			label: 'Subitem with submenu',
			submenu: [
				{
					label: 'Submenu &item 1',
					accelerator: 'Ctrl+T'
				}
			]
		}
	]
}));

titlebar.updateMenu(menu);
```

### Update Menu Position

You can change the position of the menu bar. `left` and `bottom` are allowed.

```js
titlebar.updateMenuPosition('bottom');
```

### Set Horizontal Alignment

> setHorizontalAlignment method was contributed by [@MairwunNx](https://github.com/MairwunNx) :punch:

`left`, `center` and `right` are allowed

```js
titlebar.setHorizontalAlignment('right');
```

### Dispose

This method removes the title bar completely and all recorded events.

```js
titlebar.dispose();
```

## CSS Classes
The following CSS classes exist and can be used to customize the titlebar

| Class name                  | Description                                     |
| --------------------------- | ----------------------------------------------- |
| .titlebar                   | Styles the titlebar.                            |
| .window-appicon             | Styles the app icon on the titlebar.            |
| .window-title               | Styles the window title. (Example: font-size)   |
| .window-controls-container  | Styles the window controls section.             |
| .resizer top                | Description missing                             |
| .resizer left               | Description missing                             |
| .menubar                    | Description missing                             |
| .menubar-menu-button        | Styles the main menu elements. (Example: color) |
| .menubar-menu-button open   | Description missing                             |
| .menubar-menu-title         | Description missing                             |
| .action-item                | Description missing                             |
| .action-menu-item           | Styles action menu elements. (Example: color)   |

## Goal

Electron is deprecating the remote module. It is deactivated by default and
will be removed with electron 12 in the near future. The great custom-electron-titlebar 
by AlexTorresSk has been archived recently and is not maintained anymore. 
This fork is a version for electron 10+ and uses the new userland implementation
of the remote module [@treverix/remote](https://www.npmjs.com/package/@electron/remote). 

## Contributing

Many thanks to all the people who supported Alex' project through issues and pull request.
If you want to contribute with this electron 10 fork, all the issues and pull request are welcome.

You can also buy us a coffee:

| Andreas (Treverix) | Alex |
|--------------------|------|
|<a href="https://www.buymeacoffee.com/Treverix" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a> | <a href="https://www.buymeacoffee.com/bjkGN4g" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a>
 |


## License

This fork of the archived [custom-electron-titlebar project by AlexTorresSk](https://github.com/AlexTorresSk/custom-electron-titlebar) is under the [MIT](https://github.com/Treverix/custom-electron-titlebar/blob/master/LICENSE) license.

It uses Code from the [Visual Studio Code Project](https://github.com/microsoft/vscode) which is also under the [MIT](https://github.com/Treverix/custom-electron-titlebar/blob/master/LICENSE) license.

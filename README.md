# easygenerator-plugins

Collection of plugins to extend easygenerator templates functionality.

## Development

1. Run `npm install`
2. Run `npm run build`
3. Copy `dist` directory to project that use *easygenerator-plugins* for test

## Bumping version

`npm version major | minor | patch`

This updates a version number in package.json and creates a corresponding tag on GitHub.

## How to use

### Install with [Bower](http://bower.io) 
```
bower install easygenerator-plugins
```

### Add scripts to page
```html
<body>
  ...
  <script src="[path]/easygenerator-plugins/dist/plugins.js" type="text/javascript"></script>
  ...
</body>
```

### Add styles to page
```html
<head>
  ...
  <link href="[path]/easygenerator-plugins/dist/styles.css" rel="stylesheet"/>
  ...
</head>
```

## Localization

```javascript
pluginsLocalizationService.init('en');
```

## Review plugin

Add comments functionality for course in review mode.
If the course is opened for review, the query string parameter `'reviewApiUrl'` and `'authoringToolDomain'` is added to url.

### Mark element for review

To mark an element for review, add `'reviewable'` class in the html markup.

```html
 <li class="section reviewable">
    ...
 </li>
```

### Init plugin

```javascript
var reviewPlugin = new ReviewPlugin();

 reviewPlugin.init({
                reviewApiUrl: decodeURIComponent(reviewApiUrl),
                authoringToolDomain: decodeURIComponent(authoringToolDomain),
                courseId: courseId
            });
```

When page html changes, call `renderSpots()` method.

```javascript
 reviewPlugin.renderSpots();
```

## Translation plugin

Add translate functionality to course.
This plugin will translate pages in course.

### How to use

To translate page you should have on page attributes for some cases of translation:

#### For default text translation:
```html
 <span data-translate-text="some-text-key">
    ...
 </span>
```

#### For placeholder translation:
```html
 <input type="text" data-translate-placeholder="some-text-key"/>
```

#### For title translation:
```html
 <img data-translate-title="some-text-key"/>
```

After that you should call method *localize* of global class *TranslationPlugin*:

```javascript
 TranslationPlugin.localize();
```

### Init plugin

To init plugin you must have translations object, where we have keys and current translation as value.
To init plugin just call method init with translations.

```javascript
 var translation = {
     "some-text-key1" : "My translated text 1",
     "some-text-key2" : "My translated text 2",
     "some-text-key3" : "My translated text 3"
 };

 TranslationPlugin.localize(translation);
```

## Configuration reader plugin

This plugin provide functionality of read and merge configurations from template configuration files to *ConfigurationReader* global class.
Here is list of configurations:

* manifest.json
* themeSetting.js
* publishSettins.js
* settings.js
* *.json (lang files with translations from manifest.json)

### How to use

Method *read* of *ConfigurationReader* is async and return 5 configs:

```javascript
 ConfigurationReader.read(path).then(function(configs){
     /*configs = {
         manifest: {...},
         publishSettings: {...},
         templateSettings: {...},
         themeSettings: {...},
         translations: {...}
     }*/  
     ...
 });
```
Where *path* is path to configs.

Method *init* take configs from *read* method, merge it and return 2 configs:

```javascript
 ConfigurationReader.read(path).then(function(configs){
     var settings = ConfigurationReader.init(configs);
     
     /*settings = {
         templateSettings: {...},
         translations: {...}
     }*/
     ...
 });
```

Where templateSettings is merged **templateSettings** with **themeSetting** and **defaultTemplateSettings** from manifest;
And translations is merged translations;

## Less processor plugin

The **LessProcessor** plugin provide functionality to rewrite color and font variables in not compiled less files.

### How to use

Method *load* take 3 parameters:
* colors - array with color variable keys and its value (required):
  ```javascript
    [
        {
            key: '@color1',
            value: '#f421ac3'
        },
        {
            key: '@color2',
            value: '#afafaf'
        }
    ]
  ```
* fonts - array with font variable keys and its value (required). Some variables and variables with value equal to null will be ignored:
  ```javascript
    [
        {
            "place": "google", //will be ignored
            "key": "main-font",
            "fontFamily": "Arial",
            "fontWeight": null, //used default value
            "size": null, //used default value
            "color": null, //used default value
            "textBackgroundColor": null, //used default value
            "fontStyle": null, //used default value
            "textDecoration": null, //used default value
            "isGeneralSelected": null, //will be ignored
            "isGeneralColorSelected": null //will be ignored
        }, 
        {
            "place": "google", //will be ignored
            "key": "Heading1",
            "fontFamily": "Arial",
            "fontWeight": "700",
            "size": 26,
            "color": "#666666",
            "textBackgroundColor": null, //used default value
            "fontStyle": "normal",
            "textDecoration": "none",
            "isGeneralSelected": true, //will be ignored
            "isGeneralColorSelected": true //will be ignored
        }
    ]
  ```
* path - path to less file for caching in local storage (default value - "/css/colors.less").

Just call *LessProcessor.load* with this parameters:
```javascript
    LessProcessor.load(colors, fonts);
    //or    
    LessProcessor.load(colors, fonts, path);
```

## Web font loader plugin

The **WebFontLoader** plugin provide functionality to load custom fonts from another services. For work it required *WebFont*.

### How to use

Method *load* takes 3 required parameters:
* fonts - array with fonts settings:
  ```javascript
    [
        {
            "place": "google",
            "fontFamily": "Arial",
            "fontWeight": null,
            /*not used properties...*/
        }, 
        {
            "place": "google",
            "fontFamily": "Arial",
            "fontWeight": "700",
            /*not used properties...*/
        }
    ]
  ```
* manifest - object with template settings:
  ```javascript
    {
        "fonts": [
            {
                "fontFamily": "Material Icons", //family name
                "variants": [ //family vatiants to load
                    "400"
                ],
                "url": "./css/fonts.css", //url from what it will be loaded
                "isLocal": true //is it local template font
            },
            /*another template fonts...*/
        ],
    }
  ```
* publishSettings - object with publish settings:
  ```javascript
    {
        customFontPlace: 'someurl.org/fonts.css' //url where contains custom fonts
    }
  ```

For download fonts just call *WebFontLoader.load* with relevant parameters:
  ```javascript
    WebFontLoader.load(fonts, manifest, publishSettings); //will be returned promise
  ```

## Supported browser

Easygenerator 'Browser not supported' page.

### Configuration

You must configure list of browsers you want to support.
```javascript
  supportedBrowser.configure({
    browsers: {
        win: {
            chrome: {},
            firefox: {},
            msie: {}
        },
        mac: {
            safari: {},
            chrome: {}            
        },
        android: {
            native: {
                title: 'Android Browser',
                image: 'css/img/browsers/android.png'
            },
            chrome: {
		            version: 39
	          }
        },
        ios: {
            safari: {},
            chrome: {}
        }
    },
    mainAppContainerId: 'applicationHost',
    globalBrowsersInfo: {
        chrome: {
            title: 'Google Chrome',
            link: 'http://www.google.com/chrome',
            image: 'css/img/browsers/chrome.png',
            version: 43
        },
        firefox: {
            title: 'Mozilla Firefox',
            link: 'http://www.mozilla.com/firefox',
            image: 'css/img/browsers/firefox.png',
            version: 33
        },
        msie: {
            title: 'Internet Explorer',
            link: 'http://windows.microsoft.com/en-US/internet-explorer/download-ie',
            image: 'css/img/browsers/ie.png',
            version: 11
        },
        safari: {
            title: 'Apple Safari',
            link: 'http://www.apple.com/safari',
            image: 'css/img/browsers/safari.png'
        }
    }
});
```

#### Parameters

parameter | description
--- | ---
**browsers** | list of supported browsers
**mainAppContainerId** | ID of container which will be hidden when browser not supported. It should be the highest block in the body
**globalBrowsersInfo** | when browser parameters is the same for all platforms to exclude repetition you can add global info about this browser

#### Browsers list configuration
`browsers` should contain list of supported platforms and each platform should contain list of supported browsers for this platform.
```javascript
browsers: {
    win: {
        chrome: {},
        firefox: {},
        msie: {}
    },
    mac: {
        safari: {},
        chrome: {}            
    }
}
```
Each browser must be described by the next parameters:

parameter | required | description
--- | --- | ---
**title** | required | browser title, that will be shown on the page
**image** | required | path to browser image, that will be shown on the page
**link** | optional | url to download the browser. When omitted, the browser block will be unclickable
**version** | optional | browser version from which browser supported. When ommitted all version will be supported

#### Supported platforms and browsers

#### Platforms

code | platform name
--- | ---
**win** | Microsoft Windows
**mac** | Apple Mac OS
**linux** | Linux
**android** | Google Android
**ios** | Apple IOS
**winphone** | Microsoft Windows Phone
**blackberry** | Blackberry

#### Browsers

code | browser name
--- | ---
**chrome** | Google Chrome
**firefox** | Mozilla Firefox
**safari** | Apple Safari
**msie** | Microsoft Internet Explorer
**opera** | Opera

### Initialization

After you configure plugin you must call `init` function:
```javascript
 supportedBrowser.init();
```
Also supported chaining:
```javascript
 supportedBrowser.configure({...}).init();
```

### Debug

To turn on the debug mode set `debug` parameter to true.

```javascript
 supportedBrowser.configure({
    ...,
    debug: true
 });
```
In debug mode on page load will be shown the `alert` with current browser info.
When you have problem with configuration (you don't know how current browser detected) you should turn on the debug mode.

### Resources

First, install NPM dependencies with `npm ci`. Then run

```bash
npm run resources -- --token <localise_api_token>
```
You can get API key from 1Password.

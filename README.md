# GulpEx
This package provides an easy-to-use and highly configurable wrapper for performing the most common gulp tasks like bundling JavaScript source files and compiling SASS/SCSS to CSS.

Please post bug reports and feature requests here: https://github.com/arminlinzbauer/gulpex/issues

## INDEX
1. [INSTALLATION](#installation)
1. [STANDARD TASKS](#standard-tasks)
1. [CONFIGURATION](#configuration)
1. [DEFAULTS AND FALLBACKS](#defaults-and-fallbacks)
1. [FULL GULPFILE EXAMPLE](#full-gulpfile-example)

## INSTALLATION
1. If you haven't done so already, install the gulp-cli using an elevated CMD, PowerShell (Windows) or, depending on your preference, an elevated or non-elevated shell and execute the following command: 
    ```sh
    npm install -g gulp-cli@latest
    ```
1. Install the latest version of this package by executing the following command in your project's directory: 
    ```sh
    npm install @arminlinzbauer/gulpex
    ```
1. In your project's directory, create a new file named `gulpfile.js` with the following content: 
    ```node
    const gulpex = require("@arminlinzbauer/gulpex");
    gulpex(exports, {
        // Configuration goes here...
    });
    ```

## STANDARD TASKS
GulpEx provides you with a couple of pre-defined tasks that can be executed by running the following command in the directory your `gulpfile.js` is in:
```sh
gulp <task-name>
```

TASK-NAME | DESCRIPTION
-----|-------------
init	       | Copies configured assets to their corresponding locations
convert-js  | Generates configured JavaScript bundles for development<sup>1</sup>
convert-css | Compiles configured SASS/SCSS files for development<sup>1</sup>
convert     | &rarr; (convert-js + convert-css)
deploy-js   | Generates configured JavaScript bundles for production<sup>2</sup>
deploy-css  | Compiles configured SASS/SCSS files for production<sup>2</sup>
deploy      | &rarr; (deploy-js + deploy-css)
watch       | &rarr; (convert + [recompile on change] + [reload compiled CSS-files in browser])

<sup>1</sup>&nbsp;Source maps **enabled** by default, no compression.\
<sup>2</sup>&nbsp;Source maps **disabled** by default, minified. Creates `*.min.*` files.\

Running `gulp` without any arguments will trigger the `deploy` task once.

## CONFIGURATION

To modify how GulpEx behaves and to tell GulpEx what JS-Bundles to generate & what SASS/SCSS files to compile, you can add or change the configuration object that is passed as a second parameter to the gulpex-function in your `gulpfile.js`.

The following nodes are available:

 - bundles
 - includePaths
 - assets
 
**Attention:** All paths should be specified either as absolute paths or, in case of paths relative to your project's directory, with a leading `./`.

### Bundles
To define bundles, you need to specify a unique key which can contain various properties as key-value-pairs.
Any single bundle object has the following structure:

```
uniqueKey: {
  
  type: String,           // The type of bundle. Possible values are `'script'` and `'style'`.
                          // This property is required.

  name: String,           // The name of the bundle (required for type `'script'` only).
  
  path: String,           // The output path of the generated file(s).
                          // If not specified, './html/js' (type = `'script'`) or 
                          // './html/css' (type = `'style'`) will be used.
  
  files: String[],        // The files that should be compiled / included in the bundle. Can be a glob pattern.
  
  minify: Boolean,        // Toggles the minification / uglification.
                          // (Only for deploy tasks. Minification is always off for development builds.)
                          // The default is true.
                    
  sourcemaps: Boolean     // Toggles source map generation for this bundle on or off.
                          // (Only for deploy tasks. Source maps are always on for development builds.)
                          // The default is false.
                     
  watch: string[]|Boolean // Toggles the watcher for this bundle on or off. 
                          // For bundles of type `'style'`, a list of additional files or glob patterns can
                          // be specified instead to also watch for changes on thos files without them actually
                          // being compiled.

}
```

You can specify as many bundles as you want as long as each of them has a unique key.

Oftentimes, one single , very minimal bundle definition may provide everything you need.
The following bundle definition compiles all SASS/SCSS files (that don't start with an underscore) 
within the `./sass` directory and dumps the compiled CSS files into the `./html/css` directory.

```
{
    bundles: {
        myGlobalStyleBundle: {
            type: 'style',
            files: [ "./sass/**/*.s[ca]ss" ]
        }
    }
}
```

### Include Paths
An array of additional include paths used by the node-sass compiler. 
Check their documentation for more information on this topic.

### Assets
Specifies a list of files that should be copied to a publicly accessible location (e.g. document root).
Assets can be specified in two ways (both of which support glob patterns):

**As a string**\
Source files will be copied to a pre-defined location within your project's public document root.
Without further configuration, this location will default to `./html/assets`.

**As an objekt**\
Source files will be copied from `object.src` to `object.dest`. If `object.dest` is omitted, files will again be copied to the pre-defined asset location.

## DEFAULTS AND FALLBACKS
Most of the default / pre-defined paths can be overwritten in your config object.
The following config properties (with their corresponding default values) can be overwritten:

 - `projectRoot` : `'.'`
 - `documentRoot` : `'./html'`
 - `assetDirectory` : `'./html/assets'`
 - `nodeModules` : `'./node_modules'`
 - `cssDirectory` : `'./html/css'`
 - `scriptsDir` : `'./html/js'`

## FULL GULPFILE EXAMPLE
Tying together what we've leared, a possible gulpfile configuration could look something like this:

```node
const gulpex = require('@arminlinzbauer/gulpex');
gulpex(exports, {
    cssDirectory: './html/css/dist',
    scriptsDir: './html/js/dist',
    bundles: {
        myStyles: {
            type: 'style',
            files: [ './scss/**/*.scss' ],
            sourcemaps: true
        },
        myJsLibs: {
            type: 'script',
            name: 'libraries.bundle.js',
            minify: false,
            sourcemaps: false,
            files: [
                './node_modules/jquery/dist/jquery.min.js',
                './node_modules/bootstrap/dist/js/bootstrap.min.js'
            ],
        },
        myCustomJsScripts: {
            type: 'script',
            name: 'scripts.bundle.js',
            minify: true,
            files: [ './js/**/*.js' ],
        }
    }
});

```

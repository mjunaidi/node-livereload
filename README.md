
node-livereload
===============

![Build status](https://travis-ci.org/napcs/node-livereload.svg?branch=master)

An implementation of the LiveReload server in Node.js. It's an alternative to the graphical [http://livereload.com/](http://livereload.com/) application, which monitors files for changes and reloads your web browser.

# Example Usage

You can use this by either adding a snippet of code to the bottom of your HTML pages **or** install the Browser Extensions.

## Method 1: Add browser extension

Install the LiveReload browser plugins by visiting [http://help.livereload.com/kb/general-use/browser-extensions](http://help.livereload.com/kb/general-use/browser-extensions).

Only Google Chrome supports viewing `file:///` URLS, and you have to specifically enable it. If you are using other browsers and want to use `file:///` URLs, add the JS code to the page as shown in the next section.

## Method 2: Add code to page

Add this code:

```html
<script>
  document.write('<script src="http://' + (location.host || 'localhost').split(':')[0] +
  ':35729/livereload.js?snipver=1"></' + 'script>')
</script>
```

Note: If you are using a different port other than `35729` you will
need to change the above script.

# Running LiveReload

You can run LiveReload two ways:

## Option 1: Command line

To use livereload from the command line:

```sh
$ npm install -g livereload
$ livereload [path]
```


## Option 2: From within your own project

To use the api within a project:

```sh
$ npm install livereload
```

Then, create a server and fire it up.

```js
var livereload = require('livereload');
var server = livereload.createServer();
server.watch(__dirname + "/public");
```

You can also use this with a Connect server. Here's an example of a simple server
using `connect` and a few other modules just to give you an idea:

```js
var connect  = require('connect');
var compiler = require('connect-compiler');
var static = require('serve-static');

var server = connect();

server.use(
  compiler({
      enabled : [ 'coffee', 'uglify' ],
      src     : 'src',
      dest    : 'public'
  })
);

server.use(  static(__dirname + '/public'));

server.listen(3000);

var livereload = require('livereload');
var lrserver = livereload.createServer();
lrserver.watch(__dirname + "/public");
```

You can then start up the server which will listen on port `3000`.

## Watching multiple paths:

Passing an array of paths or glob patterns will allow you to watch multiple directories. All directories have the same configuration options.

```js
server.watch([__dirname + "/js", __dirname + "/css"]);
```

Command line:

```sh
$ livereload "path1, path2, path3"
```

## Using the `originalPath` option:

```js
// server.js
var server = livereload.createServer({
    originalPath: "http://domain.com"
});
server.watch('/User/Workspace/test');
```

Then run the server:

`$ node server.js`

Then, assuming your HTML file has a stylesheet link like this:

```html
<!-- html -->
<head>
  <link rel="stylesheet" href="http://domain.com/css/style.css">
</head>
```

When `/User/Workspace/test/css/style.css` is modified, the stylesheet will be reload.

# Command-line Options

The commandline options are

* `-p` or `--port` to specify the listening port
* `-d` or `--debug` to show debug messages when the browser reloads.
* `-e` or `--exts` to include additional extentions that you want to observe. An example being -e 'jade scss'.
* `-u` or `--usepolling` to poll for file system changes. Set this to true to successfully watch files over a network.
* `-w` or `--wait` to add a delay (in miliseconds) between when livereload detects a change to the filesystem and when it notifies the browser

Specify the path when using the options.

```sh
$ livereload . -w 1000 -d
```


# API Options

The `createServer()` method accepts two arguments. 

The first are some configuration options, passed as a JavaScript object:

* `https` is an optional object of options to be passed to [https.createServer](http://nodejs.org/api/https.html#https_https_createserver_options_requestlistener) (if not provided, `http.createServer` is used instead)
* `port` is the listening port. It defaults to `35729` which is what the LiveReload extensions use currently.
* `exts` is an array of extensions you want to observe. The default extensions are  `html`, `css`, `js`, `png`, `gif`, `jpg`, `php`, `php5`, `py`, `rb`,  `erb`, and "coffee."
* `applyCSSLive` tells LiveReload to reload CSS files in the background instead of refreshing the page. The default for this is `true`.
* `applyImgLive` tells LiveReload to reload image files in the background instead of refreshing the page. The default for this is `true`. Namely for these extensions: jpg, jpeg, png, gif
* `exclusions` lets you specify files to ignore. By default, this includes `.git/`, `.svn/`, and `.hg/`
* `originalPath` Set URL you use for development, e.g 'http:/domain.com', then LiveReload will proxy this url to local path.
* `overrideURL` lets you specify a different host for CSS files. This lets you edit local CSS files but view a live site. See <http://feedback.livereload.com/knowledgebase/articles/86220-preview-css-changes-against-a-live-site-then-uplo> for details.
* `usePolling` Poll for file system changes. Set this to `true` to successfully watch files over a network.
* `delay` add a delay (in miliseconds) between when livereload detects a change to the filesystem and when it notifies the browser. Useful if the browser is reloading/refreshing before a file has been compiled, for example, by browserify.
* `noListen` Pass as `true` to indicate that the websocket server should not be started automatically. (useful if you want to start it yourself later)

The second argument is an optional `callback` that will be sent to the LiveReload server and called for the `listening` event. (ie: when the server is ready to start accepting connections)

# Changelog

### 0.6.0
* Implements LiveReload protocol v7 so browser plugins work again. 
* Removes support for protocol v6
* Introduces `noListen` option
* Introduces optional callback which will be invoked when the LiveReload server is listening

### 0.5.0
* Updated `ws` library
* Fix issues with exclusions
* Allow watching multiple paths from CLI
* Added `delay` option

### 0.4.1
* Remove some bad JS code
*
### 0.4.0
* Rewritten using Chokidar library and `ws` library
* Added `usePolling` option
* Added support for specifying additional extensions from the CLI

Older version history not kept.

# License

Copyright (c) 2010-2016 Brian P. Hogan and Joshua Peek

Released under the MIT license. See `LICENSE` for details.

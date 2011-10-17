$script.js - Async JavaScript loader and dependency manager
===========================================================

`$script.js` is an asynchronous JavaScript loader and dependency manager with an astonishingly impressive lightweight footprint. Like many other script loaders, $script.js allows you to load script resources on-demand from any URL and not block other resources from loading (like CSS and images). Furthermore, it's unique interface allows developers to work easily with even the most complicated dependencies, which can often be the case for large, complex web applications.

Features
--------
* Asynchronous & synchronous loading of scripts
* Flexible dependency management system
* No conflict mode (like jQuery's)
* No browser/javascript hacks. This guarantees your website/webapp won't break after browser updates/upgrades. 

Browser Support
---------------
* IE 6+
* Chrome 9+
* Firefox 2+
* Opera 10+
* Safari 3+

API
---

### $script()

Description: Loads a script asynchronously if the browser supports async loading of scripts. Execution order of scripts is not guaranteed.  
Contents:  

**$script( paths [, readyCallback ] )**
  
paths: A string or an array of strings containing the path to the script.  
readyCallback: A function to execute when the specified scripts are loaded.  

``` js
// Load just one script asynchronously if it's supported
$script( "http://example.com/myscript.js" );

// Load two scripts in one declaration and execute the callback when both are ready to be used
var s1 = "https://example.com/myscript1.js",
    s2 = "../myscript2.js";
    
$script( [s1, s2], function() {
    // scripts are already loaded, parsed and executed
    // do something...
} );
```
  
**$script( paths, name [, readyCallback ] )**
  
paths: A string or an array of strings containing the path to the script.  
name: A string containing a unique name to indentify the script or bundle in `$script.ready()`.  
readyCallback: A function to execute when the specified scripts are loaded.  

``` js
// A callback function
var callback = function() {
    console.log("scripts loaded!!");   
};

// Load just one script asynchronously if it's supported, give it a name & 
// execute my callback function when scripts are ready
$script( "http://example.com/myscript.js", "myscript", callback );

// Load two scripts in one declaration, give the bundle a name &
// execute my callback function when both scripts are ready
var s1 = "https://example.com/myscript1.js",
    s2 = "../myscript2.js";
    
$script( [s1, s2], "mybundle", callback );
```


### $script.ready()

Description: to-do  
Contents:  

**$script( names, readyCallback [, errorCallback] )**
  
names: A string or an array of strings containing the unique names that identify the scripts.  
readyCallback: A function to execute when the specified scripts are loaded.  
errorCallback: A function to execute when some scripts failed to load for some reason. This callback will be passed as argument an array of paths of those scripts that failed to load.  

to-do


### $script.order()

to-do


### $script.noConflict()

to-do


### $script.path()

Optionally to make working with large projects easier, there is a path variable you can set to set as a base.

``` js
$script.path('/js/modules/')
$script(['dom', 'event'], function () {
  // use dom & event
});
```

Note that this will include all scripts from here on out with the base path. If you wish to circumvent this for any single script, you can simply call <code>$script.get()</code>

``` js
$script.path('/js/modules/')
$script(['dom', 'event'], function () {
  // use dom & event
})

$script.get('http://example.com/base.js', function () {

})
```

Examples
--------

old school - blocks CSS, Images, AND JS!

``` html
<script src="jquery.js"></script>
<script src="my-jquery-plugin.js"></script>
<script src="my-app-that-uses-plugin.js"></script>
```

middle school - loads as non-blocking, but has multiple dependents

``` js
$script('jquery.js', function () {
  $script('my-jquery-plugin.js', function () {
    $script('my-app-that-uses-plugin.js')
  })
})
```

new school - loads as non-blocking, and ALL js files load asynchronously

``` js
// load jquery and plugin at the same time. name it 'bundle'
$script(['jquery.js', 'my-jquery-plugin.js'], 'bundle')

// load your usage
$script('my-app-that-uses-plugin.js')


/*--- in my-jquery-plugin.js ---*/
$script.ready('bundle', function() {
  // jquery & plugin (this file) are both ready
  // plugin code...
})


/*--- in my-app-that-uses-plugin.js ---*/
$script.ready('bundle', function() {
  // use your plugin :)
})
```

Exhaustive list of ways to use $script.js
-----------------------------------------

``` js
$script('foo.js', function() {
  // foo.js is ready
})


$script(['foo.js', 'bar.js'], function() {
  // foo.js & bar.js is ready
})


$script(['foo.js', 'bar.js'], 'bundle')
$script.ready('bundle', function() {
  // foo.js & bar.js is ready
})


$script('foo.js', 'foo')
$script('bar.js', 'bar')
$script
  .ready('foo', function() {
    // foo.js is ready
  })
  .ready('bar', function() {
    // bar.js is ready
  })


var dependencyList = {
    foo: 'foo.js'
  , bar: 'bar.js'
  , thunk: ['thunkor.js', 'thunky.js']
}

$script('foo.js', 'foo')
$script('bar.js', 'bar')

// wait for multiple depdendencies!
$script.ready(['foo', 'bar', 'thunk'], function() {
  // foo.js & bar.js & thunkor.js & thunky.js is ready
}, function(depsNotFound) {
    // foo.js & bar.js may have downloaded
    // but ['thunk'] dependency was never found
    // so lazy load it now
    depsNotFound.forEach(function(dep) {
      $script(dependencyList[dep], dep)
    })
  })
```

Developers
----------
Building a $script environment works like this:

    $ npm install --dev
    $ make

Ender support
-------------
You can add `$script` to your existing [ender](http://ender.no.de) build by adding it as such:

    $ ender add scriptjs

Get access to it as such:

``` js
$.require(...)
```

You can also require it as such:

``` js
var $S = require('script')

$S('/foo.js', function () {
  // foo is ready
})
```

Contributors
------------
  * [Dustin Diaz](https://github.com/ded/script.js/commits/master?author=ded)
  * [Jacob Thornton](https://github.com/ded/script.js/commits/master?author=fat)
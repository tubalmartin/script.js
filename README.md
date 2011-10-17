$script.js - Async JavaScript loader and dependency manager
===========================================================

`$script.js` is an asynchronous JavaScript loader and dependency manager with an astonishingly impressive lightweight footprint. Like many other script loaders, $script.js allows you to load script resources on-demand from any URL and not block other resources from loading (like CSS and images). Furthermore, it's unique interface allows developers to work easily with even the most complicated dependencies, which can often be the case for large, complex web applications.

Features
--------
* Asynchronous loading of scripts
* Possibility to specify execution order of scripts
* Avoids duplicate downloads of scripts already requested/downloaded
* Powerful dependency management system
* No-conflict mode (like jQuery's)
* No browser/javascript hacks. This guarantees your website/webapp won't break after browser updates/upgrades.
* Super lightweight: 1.659 kb minified.

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

*Description:*   
- Loads a script asynchronously if the browser supports async loading of scripts. Execution order of scripts is not guaranteed.  

*Returns*   
`$script` object

*Contents:*  

**$script( paths [, readyCallback ] )**

*Arguments*  
- paths: A string or an array of strings containing the path to the script.  
- readyCallback: A function to execute when the specified scripts are loaded.  

``` js
var s1 = "https://example.com/myscript1.js",
    s2 = "../myscript2.js";

// Load just one script asynchronously if it's supported
$script( "http://example.com/myscript.js" );

// Load two scripts in one declaration and execute the callback when both are ready to be used 
$script( [s1, s2], function() {
    // scripts are already loaded, parsed and executed
    // do something...
} );
```
  
**$script( paths, name [, readyCallback ] )**

*Arguments*  
- paths: A string or an array of strings containing the path to the script.    
- name: A string containing a unique name to indentify the script or bundle in `$script.ready()`.    
- readyCallback: A function to execute when the specified scripts are loaded.  

``` js
var s  = "http://example.com/myscript.js",
    s1 = "https://example.com/myscript1.js",
    s2 = "../myscript2.js",
    callback = function() {
        console.log("scripts loaded!!");   
    };

// Load just one script asynchronously if it's supported & give it a name 
// to identify it in $script.ready(name, callback) calls
$script( s, "myscript" );

// Load two scripts in one declaration, give the bundle a name &
// execute my callback function when both scripts are ready
$script( [s1, s2], "mybundle", callback );
```


### $script.ready( names, readyCallback [, missingCallback] )

*Description*   
- Attaches a handler to "onload" and "onreadystatechange" events for the specified script names.  
- This handler will execute when the specified scripts are loaded.  
- You can have multiple calls to `$script.ready()` for the same dependency (script name).  
- You can call `$script.ready()` anywhere in your code even if a script "name" has not been set yet.  

*Returns*   
`$script` object

*Arguments*  
- names: A string or an array of strings containing the unique names that identify the scripts.    
- readyCallback: A function to execute when the specified scripts are loaded.    
- missingCallback: A function to execute when a specified script is missing or, more technically, not internally registered as requested (to be loaded). An array of script names will be passed to the first argument of the callback. All names will be passed, not only those of the scripts that are not registered yet. This library expects scripts' paths/urls to be valid (no 404/filesystem errors).         
                   
``` js
var s  = "http://example.com/myscript.js",
    callback = function() {
        console.log("scripts loaded!!");   
    },
    showLove = function(){
       console.log("I love $script.js!!");
    }; 

// Load just one script asynchronously if it's supported & give it a name 
// to identify it in $script.ready(name, callback) calls
$script( s, "myscript" );

// When "myscript" is loaded (ready to be used) execute these callbacks 
$script.ready( "myscript", callback )
       .ready( "myscript", showLove );
```       

``` js
var myScripts = {
        "jquery": "https://ajax.googleapis.com/ajax/libs/jquery/1.4.4/jquery.min.js",
        "my-jquery-plugins": "http://mysite.com/my-jquery-plugins.js",
        "my-app-code": "http://mysite.com/my-app-code.js"
    },
    
    readyCallback = function() {
       console.log("All my scripts are loaded!!!");
    },
    
    // If any script is missing (not registered yet, but required to be ready), lazy load it now
    missingCallback = function( names ) {
       for (var name in myScripts) {
       	  // Do not worry about duplicate downloads since all scripts that are already loaded 
       	  // will not be requested or downloaded again.		
          $script( myScripts[ name ], name ); 
       }
    };

// At some point in our code we load 2 out of 3 our scripts
$script( myScripts["jquery"], "jquery" );
$script( myScripts["my-jquery-plugins"], "my-jquery-plugins" );

// Let's say at this point I need all 3 of my scripts to be ready. 
// Since we have not requested to load my third script named "my-app-code", 
// the 'missingCallback' function will be called
$script.ready( [ "jquery", "my-jquery-plugins", "my-app-code"  ], readyCallback, missingCallback);    
```


### $script.get()

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

When not to use $script.js
--------------------------

**document.write()**

Don't use $script.js with scripts that have document.write() in them. At least not yet. document.write() is natively synchronous and thus will break with the asynchronous loading techniques inherent to $script.js. 

**Certain frameworks which have unsafe DOM-ready detection**

There are scripts that do DOM-ready detection, such as jquery.js. Unfortunately, some of these methods for DOM-ready detection are flawed in one specific case -- when the file with the DOM-ready detection loads in a page AFTER the DOM-ready has already happened.  
They do a bad job of detecting that this is the case in some browsers, and so, wait "forever" thinking they are still waiting for DOM-ready to occur.  
This means that any code which is queued up waiting for DOM-ready to occur never gets executed.

So, if you load such a script dynamically with $script.js, and you have optimized the rest of your page in such a way that DOM-ready happens before that script gets there, then your page will be left in an uninitialized state because DOM-ready will have passed and will not fire a second time.

jQuery 1.3.2 and below suffers from this problem, as do some others. However, a bug was filed with jQuery about this, and a fix has been put in for jQuery 1.4. So jQuery 1.4+ will be fine to load with $script.js. But 1.3.2 and below should not be loaded with $script.js because of this race condition between script loading and the actual DOM-ready event.

There are two simple fixes to this issue, though:
* Option 1) Simply load ONLY the jquery.js (1.3.2 and below) script file (or whatever other script that has its own unsafe DOM-ready detection logic in it) manually, using a regular script tag. This will guarantee that its internal DOM-ready detection will occur correctly, since the script tag you load it with will block DOM-ready for it to load first. Once jQuery has registered DOM-ready, all other plugins and blocks of $(document.ready(function(){...}); that queue for the DOM-ready event will work fine, even though you load them with $script.js.
* Option 2) Place a "bootstrap" script (one that contains $script.js and loads all your scripts) manually, using a regular script tag, just before the closing </body> tag (bottom of body). This way you know that by the time your scripts load, the DOM will be ready.


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

List of ways to use $script.js

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
#### Barrel Development Best Practices
# JavaScript Best Practices

### Goals and Questions You Should Ask Yourself
1. Maintainability 
  - is this going to make sense in 3 months?
  - what can I document now, to save time later?
  - how can I simplify this?
2. Readablity
  - *contributes to \#1*
  - are my teammates going to understand this?
  - what can I do to help my partner use this to their advantage?
3. Flexibility
  - can I use this on other projects?
  - can I use this elsewhere within your current project?
  - what if this needs to change later?
  - what pieces can I abstract from this piece of code?
4. Speed
  - can I make this just as readable/maintainable in vanilla js?
  - what are the performance impacts of this code?

* * *

## Structure
```bash
# in src/js/ directory
lib/
vendor/ (optional)
main.js
  
# in modules/
hero/
  |– hero.js
  |- hero.handlebars
slideshow/
  |- slideshow.js
  |– slideshow.handlebars
```

#### Root File (`main.js`) 
The main javascript file serves as the entry point for the Browserify bundle. This file should pull in global dependencies for the site and initialize them on page load if necessary.

#### Lib Directory
The lib folder contains re-usable functions that can be used in your individual modules.

#### Modules Directory
The modules folder contains templates and associated JavaScript functions which are initialized on load using the `data-module-init` attribute.

## Workflow 
1. Import dependencies via NPM
  - maintain a clean `package.json`
  - Install front-end plugins to `dependencies` using `npm install --save`
  - Install gulp plugins and other build tools to `devDependencies` using `npm install --save-dev`
2. Build using Gulp
  - see Barrel Base for boilerplate
3. Use Browserify to bundle modules
  - if a library is not on NPM, use Browserify Shim or similar to transform into a common-js module
  - declare all dependencies at the top of each file, via `require()`
  - module files should export a function using `module.exports`
4. Use uglify to compress files production files
  - skipping compression during dev using a separate build task is ideal
5. Modules that require javascript should have their own JS partial, which are instantiated via our `data-module-init` pattern inside `main.js` on page load.
  - see Barrel Base for examples

## Styleguide
- Use vanilla javscript where possible
  - if using jQuery, include as a `browser` dependency in your `package.json` and include it via `var $ = require('jquery')` in your `main.js` (et al) file.
- Use of ES6 is OK, but _document_ the build steps you are using 
  - use Babel to transpile
  - do not use anything that is not on the standards track, i.e. anything beyond the ES2015 (ES6) spec, like ES2016, ES2017
- Use semi-colons for consistency
  ```javascript
  function log(){
    // like this
    console.log(str);

    // not like this
    console.log(str)
  }
  ```
- Trailing commas + list un-assigned variables first
  ```javascript
  var unassigned, 
      assigned = 'This variable is assigned';
  ```
- When writing ternary, use parentheses to mark the left-hand side argument
  ```javascript
  var duration = (speed === 'fast') ? 200 : 800;
  ```
- Use named functions always, it helps with debugging
  ```javascript
  // this is an anonymous function
  var log = function(msg){
    console.log(msg);
  }

  // this is a named function
  function log(msg){
    console.log(msg);
  }
  ```

## Feature Detection
Use a _custom_ build of Modernizr when necessary. Ensure the generated URL is not stripped from the source code, so that the package can be amended in the future if needed.

## Things to Keep In Mind

#### You don't need to declare everything within `$.ready()`.
Only declare what initiates your application within the `.ready()`. Keep everything else scoped and modularlized as needed.

```javascript
function App(){
  ...
}

// Or $(document).ready();
jQuery(function($){
  new App();
});
```

#### Avoid unecessary `if ... else` statements.
While they are very explicit, they add unecessary visual clutter in large files. Use ternary for simple value checking. Where multiple levels are needed, `if ... else` is still preferable.

#### When to use `switch()` statements.
TODO

## Advanced Techniques
#### TODO
1. Rendering pipeline
2. RAIL principles
3. Web workers

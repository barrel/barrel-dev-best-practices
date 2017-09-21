### 📚 Table of Contents
- Base Rules
- Bundle Structure
- Module File Structure
- Compiling
- Common jQuery Shims

> #### ✅ ES6 and beyond over jQuery
> jQuery provides a set of utilities to fill in the blanks of a once minimal native language. Modern day javascript and transpilers such as Babel make it much easier do the tasks jQuery helps with, such as traverse the DOM and make AJAX calls. Please utilize new Javascript standands and transpilers instead of adding jQuery to a project. See [section below](#juery-shims) for examples of non-jquery replacements

***
#### 📍 Base Rules

#### 1️⃣ Keep module files succinct
Consider your code from the perspective of another engineer coming in with no prior knowledge of the project. Short, succinct code is much easier to review than long verbose code. A module that gets the same job done in less lines of code is a end goal to work towards. See below for a number of techniques that can help you tighten up your code.

**Abstract common tasks to utility functions**

```javascript
// Bad
const parent = [].slice.call(document.querySelectorAll('.js-parent'))
const child = [].slice.call(parent.querySelectorAll('.js-child'))
// ...

// Good
// This utility function can be used across multiple modules
const select = (selector, parent = document) => (
    [].slice.call(
        parent.querySelectorAll(selector)
    )
)

const parent = select('.js-parent')
const child = select('.js-child', parent)
// ...
```

**Consider using tenery operators** (When it doesn't detract from code readability)

```javascript
// Bad 
scroll(y => {
    if (y >= 10) {
        document.body.classList.add('is-fixed')
    } else {
        document.body.classList.remove('is-fixed')
    }
})

// Good
scroll(y => {
    document.body.classList[y >= 10 ? 'add' : 'remove']('is-fixed')
})
```

**Consider using arrow functions instead of functional expressions**

```javascript
// Bad
const expression = function(foo, bar) {
    return foo * bar
}

// Good
const expression = (foo, bar) => foo * bar
```

**Consider using default parameter syntax**

```javascript
// Bad
const expression = opts => {
    if (opts === void 0) {
    opts = {}
  }
}

// Bad
const expression = opts => {
    opts = opts || {}
}

// Good
const expression = (opts = {}) => {
    // ...
}
```



#### 2️⃣ Keep nesting to a minimum

High degrees of nesting creates increased line sizes and control flow confusion that detracts from code readability. The simpler the code, the easier it is to identify issues later on in the project, especially through the eyes of another developer. Restricting yourself to writing code that limits nesting to 2 or 3 indents deep helps maintain a more digestable codebase.

```javascript
// Bad
document.addEventListener( 'DOMContentLoaded', () => {
    [].slice.call(document.querySelectorAll('.js-selector')).forEach(el => {
        if (el.classList.contains('js-active')) {
            el.addEventListener('click', e => {
                document.querySelector('.js-target').classList.add('is-visible')
            })
        }
    })
})

// Good
const init = () => {
    select('.js-selector').forEach(assignListener)
}

const select = (selector, parent = document) => (
    [].slice.call(
        parent.querySelectorAll(selector)
    )
)

const assignListener = el => {
    if (el.classList.contains('js-active')){
        el.addEventListener('click', handleEvent)
    }
}

const handleEvent = e => {
    select('.js-target').classList.add('is-visible')
}

document.addEventListener('DOMContentLoaded', init)
```


#### 3️⃣ Keep a handle on your bundle size (the smaller the better)

This is something to keep in mind right from when you are providing feedback on designs. A developer should be considering the amount of vendor libraries that will be needed to satisfy the designs and the load time increase that these libraries will introduce. 

Some common questions to ask yourself:
- Can these animations be acheived through CSS or will I need to introduce an animation library?
- Do I need to add React to this project or will I better be served using Preact *or* writing this code without a framework?
- I see there are 3 slideshow variations. Can these variations be consolidated or can I find a slideshow library that will support all 3?

#### 4️⃣ Understand the browser compatibility of your code

Ask yourself *"Will the supported browsers outlined in the SOW support this?"* whenever you utilize a new method or Javascript object. Babel will transpile a lot of ES2015 with the ES2015 preset but it doesn't provide support for everything. If you are going to utilize the Fetch specification, Promises, Object.assign or other new standards, make sure that there is an appropriate polyfill added in the build process. 

#### Always lint your code using standardJS rules

***

#### 📍 Bundle Structure


```bash
# in src/js/ directory
lib/
modules/
main.js
  
# in modules/
hero.js
slideshow.js
```

##### Root File (`main.js`) 
The main javascript file serves as the entry point to the javascript bundle. This file should read like a contents page to the js bundle. It should be extremely simple, focusing primarily on including and instantiating modules.

##### Lib Directory
The lib folder contains module agnostic code that can be utilized by more than one module. For example, a ```utils.js``` file that exports a series of helper functions used across the site or a lazy-loading script that can be called to lazy load images across the site.

##### Modules Directory
The modules folder contains the javascript logic of modules used across the site. Typically, a module file will have a corresponding markup file (e.g. a liquid, PHP or HTML file), and a corresponding stylesheet file (e.g. a CSS or SASS file).

***
#### 📍 File Structure
As a guide only, regular common.js modules should be written in the following structure:

```javascript
// Include any vendor dependencies
import select from 'dom-select'

// Include any local dependencies (.e.g. utils.js)
import productCell from 'product-cell.js'

// Declare any DOM references
const slideshow = '.js-slideshow'

// Write module code
const init = () => {
    const el = select(slideshow)
}

// Declare the module exports
export default init
```

***
#### 📍 Compiling
Utilize Webpack to build, transpile and minify your bundle. Typically, there are two Webpack config files, ```webpack.config.js``` (dev) and ```webpack.production.config.js``` (production). The dev file is used when writing code while the production file is used to prepare the js bundle for production. The dev file will watch files, transpile files and serve files to the dev server. The production file will optimize files and write them to the file system. 

***
#### 📍 jQuery Shims
1. Ajax: Use [ES6 Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) or [nanoajax](https://github.com/yanatan16/nanoajax)
2. Dom References: [documentSelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll)

### 📚 Table of Contents
- Base Rules
- Using jQuery
- Bundle Structure
- Module File Structure
- Compiling

```
// Possible ads:
- DOM References
- Beware of race conditions
- Don't use global scope
```

***
#### 📍 Base Rules
1. Keep module files succinct and short
2. Keep nesting to a minimum
3. Leverage native functions over vendor libraries
4. Understand the browser compatibility of your code
5. Always lint your code using standardJS rules

***
#### 📍 ES6 and beyond over jQuery
jQuery provides a set of utilities to fill in the blanks of a once minimal native language. Modern day javascript and transpilers such as Babel make it much easier do the tasks jQuery helps with, such as traverse the DOM and make AJAX calls. Please utilize new Javascript standands and transpilers instead of adding jQuery to a project.

***
#### 📍 Bundle Structure


```bash
# in src/js/ directory
lib/
modules/
main.js
  
# in modules/
hero.js
PLP/
  |– app.js
  |- reducers.js
  |- actions.js
  |- component-1.js
  |- component-2.js
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

## Compiling
Utilize Webpack to build, transpile and minify your bundle. Typically, there are two Webpack config files, ```webpack.config.js``` (dev) and ```webpack.production.config.js``` (production). The dev file is used when writing code while the production file is used to prepare the js bundle for production. The dev file will watch files, transpile files and serve files to the dev server. The production file will optimize files and write them to the file system.


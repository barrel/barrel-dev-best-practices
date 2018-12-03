# Nolte/Barrel Best Practices() {

The Barrel approach to coding websites..

### 📚 Table of Contents
- **Languages**
    - [Javascript](/languages/javascript.md)
    - [CSS](/languages/css.md)
    - [PHP](/languages/php.md)
    - [HTML](/languages/html.md)
- **Platforms**
    - [Shopify](/platforms/shopify.md)
    - [Wordpress](/platforms/wordpress.md)
- **Foundations**
    - [SEO](/foundations/seo.md)
    - [User Experience](/foundations/seo.md)
    - [Performance](/foundations/seo.md)

* * *

[Formatting Guidelines](formatting-guidelines.md) for this document.

* * *

#### 📍 Goals and Questions You Should Ask Yourself For Every Project
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
  - what are the performance impacts of this code?

* * *

### 📍 General Practices
All projects regardless of the langauges and technologies used have a few common rules and best practices to follow. Please review the individual topics above for more specific cases.


#### String Manipulation
When outputting strings from within code, be sure to use string formatting syntaxes to allow for better readability. See the following code samples.

**PHP**
```php
$string_formatted = sprintf("Welcome, %s. Today is %s", $username, date('Y-m-d'));
echo $string_formatted;
// or to output immediately...
printf("Welcome, %s. Today is %s", $username, date('Y-m-d'));
```

**JS** — javascript has no native string formatting methods but several [node modules](https://www.npmjs.com/search?q=string+format) exist

*using [`js-string-format`](https://www.npmjs.com/package/js-string-format) python clone*

```javascript
var string_formatted = 'Welcome, {0}. Today is {1}'.format([data.username, new Date().toString()])
```

*using [`sprintf`](https://github.com/alexei/sprintf.js) php clone*
```javascript
var string_formatted = sprintf("Welcome, %s. Today is %s", data.username, new Date().toString());
```

#### Documentation
All code should use a commenting syntax that is parsable and standard. Most of the comment-parsers utilize a java-doc-style syntax. For php, this is phpdoc; for javascript, this is jsdoc; for java, this is javadoc; and for ruby, this is rubydoc. See [Documentation Guidelines](documentation.md) for more details.

#### Attribution
All code should be attributed to "Barrel" unless imported from an existing project. That is to say, you should never include yourself as an author unless you wrote something at your leisure outside of Barrel for inclusion in a project. This includes all files such as stylesheets, javascript, and template files. We have a vast git history to know who wrote what.

An example of such file header from WordPress:
```
/*
Plugin Name: WooCommerce Fulfillment
Plugin URI: http://www.woothemes.com/woocommerce/
Version: 1.0
Description: Add custom fulfillment support to WooCommerce.
Author: Barrel
Author URI: http://barrelny.com/
Text Domain: woo-fulfillment
*/
```
PHP or Javascript:
```
/**
 * This is a comment block in php or javascript
 * @author BarrelNY
 */
```

This includes any @author comment.

* * *

#### 📍 Production Basics

* All production code should produce assets that are fully compiled or minified and concatenated. See the [Javascript](javascript.md) guide for specific approaches towards including scripts.
* Limit usage of any framework and configure any frameworks to use as much code as is needed such as only including the components of bootstrap that will be used for your project.

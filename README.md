Barrel Development Best Practices
=================================

### List of Topics

- [API/REST/Server](restful.md)
- [CSS & Pre-processing](css.md)
- [Documentation Guidelines](documentation.md)
- [Git](git.md)
- [HTML](html.md)
- [HTTPS](https.md)
- [Javascript](javascript.md)
- [PHP](php.md)
- [Responsive](responsive.md)
- [Ruby / RoR](ruby.md)
- [Shopify](shopify.md)
- [WordPress](wordpress.md)
- [SEO](seo.md)
- [Task Runners](grunt.md)
- [VM & Testing](http://infocenter.barrelny.com/how-to-browser-testing-procedures/)

[Formatting Guidelines](formatting-guidelines.md) for this document.

### General Practices
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

#### Production

* All production code should produce assets that are fully compiled or minified and concatenated. See the [Javascript](javascript.md) guide for specific approaches towards including scripts.
* Avoid unnecessary style overrides in favor of specificity and @media 
* Limit usage of any framework and configure any frameworks to use as much code as is needed such as only including the components of bootstrap that will be used for your project.

#####TODO
- Add BP for form management, handling, validation
- Add BP for testing of all types
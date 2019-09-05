### Barrel Development Best Practices

We are now using [PSR-1](http://www.php-fig.org/psr/psr-1/) coding standards and [PSR-2](http://www.php-fig.org/psr/psr-2/) style standards for PHP. Please read these standards before reviewing the recommendations below.

The practices below may be used as a fallback and are acceptable alternatives to PSR-1 and PSR-2 only in the instances outlined. This may help in the cases of working with legacy PHP or supporting older applications.

Additionally we are adopting a rule that all code must be well-documented. All comments and headers must use [phpdoc](http://www.phpdoc.org/docs/latest/index.html)-styled formatting syntax to allow for enhanced readbility and quick API documentation of site/app code. This includes documenting expected arguments and output of every function.

---

# PHP Best Practices

_Because each PHP framework approaches code quality in a different way, it is important to respect the best practices and style guidelines for each framework on a case-by-case basis (ex. Wordpress, Laravel, Magento). The formats listed below highlight long-standing practices for PHP and should be used as a general guideline._

## Style

### Classes and Methods

Method names should be descriptive of their actions, using the platform or framework's existing conventions.

[WordPress](https://make.wordpress.org/core/handbook/best-practices/coding-standards/php/):
```php
class Some_Class {
    public sample_method {
        return true;
    }
}
function sample_func( $str )
{
    return strtoupper( $str );
}
```

[Laravel](https://laravel.com/docs/5.4/contributions#coding-style) follows [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md):
```php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleMethod($a, $b = null)
    {
        if ($a === $b) {
            bar();
        } elseif ($a > $b) {
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
```

#### Variables

##### Globals

Avoid using global variables, instead use [dependency injection](https://stackoverflow.com/questions/130794/what-is-dependency-injection) to pass the required data to the dependent method.

## Syntax

### Short open tags

[PHP tags](http://www.php.net/manual/en/language.basic-syntax.phptags.php)

**Do not** use short open php tags. The best practice is to use the full `<?php` open tag verses the short open tag `<?`. This is because the abbreviated version requires `short_open_tag = On` in php.ini, and access to php.ini may not be available in the production environment. It also confuses servers since other markup and template languages like xml also use `<?` to open their document context.

### Short open *echo* tags
Use of the short echo tag, `<?=`, is permissable but can only be safely used on a server with PHP 5.4 or higher. If the feature is ever removed from php or a php downgrade is required, a global find a replace can be performed. We use this in the context of simple one-line echo statements only. All other cases should use the full php open tags with a corresponding `print` or `echo` call.

### Accidental whitespace

Files that are pure PHP should have `<?php` on line one and should not have any new lines or white space at the top of the file. It is also best practice to omit the closing PHP tag at the end of the file in order to avoid the output buffer from sending the output unintentially.

### Checking value not false or empty

We often need to check if a custom field is populated, was unpopulated, or doesn't even exist. This *could* be written as 3 conditions to check if not null, not an empty string, and not false. Instead use the `empty()` to check if the variable has an empty value empty string, 0, NULL or False with 1 method.

**Do**
```php
    if ( !empty($var) ) :
```

**Do not**
```php
    if ( isset($var) && $var !== '' && $var !== false ) :
```

### Control Structures within Classes

When writing classes, functions, control, or conditional structures with PHP, please use curly braces exclusively rather than mixing with the alternative syntax for control structures (note on exception below). Whether or not curly braces stand on their own or inline with the block is purely conventional. The more important thing is that they be consistent within a programming language across the project.

```php
<?php
class WP_Post {
    // curly inline
}

function test_function
{
    // curly new line
}

if ( is_array( array() ) ) {
    // curly inline
}

foreach( array() as $k => $v )
{
    // curly new line
}
```
Again, consistent use of one or the other is prefferable.

### Templating with PHP

[Alternate syntax for control structures](http://us1.php.net/alternative_syntax)

When templating with PHP, it is best practice to use alternate syntax for the `if`, `while`, `for`, `foreach`, and `switch` control structures. Generic `}` closing tags are non-descriptive, and use of alternate syntax helps clarify the end of events. For example:

```php
<section id="Blog">
  <?php if ($articles) { ?>
    <?php foreach ($articles as $article){ ?>
      <h3><?= $article['title']; ?></h3>
      <?= $article['content']; ?>
    <?php } ?>
    
    <?php if (!is_home()) { ?>
      <p><a href="#home" title="Home">Go home</a></p>
    <?php } ?>
  <?php } ?>
</section>
```

Is better written as:

```php
<section id="Blog">
  <?php if ($articles) : ?>
    <?php foreach ($articles as $article): ?>
      <h3><?= $article['title']; ?></h3>
      <?= $article['content']; ?>
    <?php endforeach; ?>
    
    <?php if (!is_home()) : ?>
      <p><a href="#home" title="Home">Go home</a></p>
    <?php endif; ?>
  <?php endif; ?>
</section>
```

## File Stucture

### Dependencies

Instead of a vendor folder with downloaded scripts included in your repo, opt for well maintained open source libraries and install via [composer](https://getcomposer.org).

## Security

### SQL

Use [prepared statements](http://php.net/manual/en/pdo.prepared-statements.php) on SQL statements with user input data to protect your database from from SQL injection. 

### Sanitization 

Use [PHP input filtering](http://www.php.net/manual/en/function.filter-input.php) to sanitize any user input that will be printed onto the page to protect your site from from XSS. For example:

```php
<form method='get' action='index.php'>
<input name="search" value="<?php echo $_GET['search'];?>" />
<input type=submit name='getdata' value='Search' /></form>
```

Should be:

```php
<?php
$search = filter_input(INPUT_POST | INPUT_GET, 'search', FILTER_SANITIZE_SPECIAL_CHARS);
?>
<form method='get' action='index.php'>
<input name="search" value="<?php echo $search;?>” />
<input type=submit name='getdata' value='Search' /></form>
```

### Sensitive Data

Avoid including sensitive data directly in code (API keys, database credentails, etc.) and instead use [environment varibles](https://scotch.io/tutorials/how-to-use-environment-variables) to store that information in git-ignored platform-specific config files and configuration areas. Check the host or platform documentation for how to configure environment variables for that environment.

## Performance

### Monitoring

Use [New Relic](https://newrelic.com) to monitor performance during development and after releases to look for bottlenecks and bugs.

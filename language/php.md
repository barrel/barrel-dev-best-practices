### Barrel Development Best Practices

We are now using [PSR-1](http://www.php-fig.org/psr/psr-1/) coding standards and [PSR-2](http://www.php-fig.org/psr/psr-2/) style standards for PHP. Please read these standards before reviewing the recommendations below.

The practices below may be used as a fallback and are acceptable alternatives to PSR-1 and PSR-2 only in the instances outlined. This may help in the cases of working with legacy PHP or supporting older applications.

Additionally we are adopting a rule that all code must be well-documented. All comments and headers must use [phpdoc](http://www.phpdoc.org/docs/latest/index.html)-styled formatting syntax to allow for enhanced readbility and quick API documentation of site/app code. This includes documenting expected arguments and output of every function.

---

# PHP Best Practices

_Because each PHP framework approaches code quality in a different way, it is important to respect the best practices and style guidelines for each framework on a case-by-case basis (ex. Wordpress, Laravel, Magento). The formats listed below highlight long-standing practices for PHP and should be used as a general guideline._

## Style

### Control structures

Conditional control structures `if`, `while`, `for`, `foreach`, and `switch` should have white space following the structure and the condition for readability.

Wrong:
```php
  if($foo == $bar){
    foreach($foo as $f){
      return $f;
    }
  }
```

Right:
```php
  if ($foo == $bar) {
    foreach ($foo as $f) {
      return $f;
    }
  }
```

### Methods

Method names should be descriptive of their actions and all lowercase, with underscores separating words. Method parameters should be comma separated with white space to improve readability.

Wrong:
```php
function echoRoll($str,$int,$bool)
{
  echo 'Fox, do a barrel roll!';
}
```

Right:
```php
function tell_fox_to_roll($str, $int, $bool)
{
  echo 'Fox, do a barrel roll!';
}
```

### Private and protected class variables

It is helpful to prefix an underscore `_` to private or protected varaible names so that non-encapsulated variables can be easily identified. For instance:

```php
Class Foo
{
  protected $_foo;    // Available to Foo and extensions of Foo
  private $_bar;      // Only available to Foo
  public $pub;        // Available to everyone
  
  // Construct
  function __construct($str, $int, $bool)
  {
    $this->_foo = $str;
    $this->_bar = $int;
    $this->pub = $bool;
  }
  
  // Encapsulates $_bar
  protected function return_bar(){
    return $this->_bar;
  }
}

Class Bar extends Foo
{
  // Construct
  function __construct($str, $int, $bool){
    parent::__construct($str, $int, $bool); // Constructs Foo
  
    $bar = $this->return_bar(); // Retrieves $_bar
    $this->_foo = $bar; // Changes $_foo to $bar
  }
}

$foobar = new Bar('hello world', 5, TRUE);
$foobar->pub = FALSE; // Changes $_pub to FALSE
```

## Syntax

### Short open tags

[PHP tags](http://www.php.net/manual/en/language.basic-syntax.phptags.php)

Typically, it is best practice to use the full `<?php` open tag verses the short open tag `<?`. This is because the abbreviated version requires `short_open_tag = On` in php.ini, and access to php.ini may not be available in the production environment.

Use of the short echo tag, `<?=`, reqires activation only in PHP instances < 5.4, and can be safely used on any server using PHP 5.4 or where `short_open_tag` is set to true.

### Accidental whitespace

Files that are pure PHP should have `<?php` on line one and should not have any new lines or white space at the top of the file. It is also best practice to omit the closing PHP tag at the end of the file in order to avoid the output buffer from sending the output unintentially.

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

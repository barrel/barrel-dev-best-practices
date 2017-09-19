### Barrel Development Best Practices
- [Config for Development](#config-for-development)
- [Using Plugins](#using-plugins)
- [DRY](#dont-repeat-yourself)
- [Action and Filter Hooks](#wordpress-action-and-filter-hooks)
- [Templates, Modules, Components, and Partials](#templates-modules-components-and-partials)
- [Images](#images)
- [Rewrites](#rewrites)
- [Performance & Security](#performance-security-considerations)
- [APIs](#apis)
- [Preferred Plugins](#preferred-plugins)


# WordPress Best Practices
 
## Config For Development

Within your `wp-config.php` in a block reserved for non-production developemnt environments, override some defaults to optimize for fastest use.

Set the frequency of the trash being emptied to zero. This effectively removes the trash functionality, and all posts, when deleted, will be gone—no trace of them in the database.

`define( 'EMPTY_TRASH_DAYS', 0 ); // Zero days`

Do not suppress error logging and output. This often helps if you are using third party plugins as a dependency. You will then see errors pop up a lot more, which give good insight as to whether the plugin code is being maintained. WordPress constantly let’s you know when certain functions are deprecated.

`define( 'WP_DEBUG', true );`

In fact, report as many errors as you can unless third-party plugins make this impossible.

`error_reporting(E_ALL); // Report all PHP errors`

Disable or reduce post revisions. This reduces the amount of database entries.

`define( 'WP_POST_REVISIONS', false ); // Or an integer fewer than 3`

Increase this setting for longer delays in between auto-saves. The default is 60 seconds.

`define( 'AUTOSAVE_INTERVAL', 160 ); // Seconds`

Much more can be done with error logging to effect good debug. See more on [Codex](https://codex.wordpress.org/Editing_wp-config.php).

## Using Plugins

### When to Use a Plugin
When a large piece of functionality not already provided in WordPress is well-supported, widely received (*10K+ installs*), highly rated (*3+ star ratings*), and regularly maintained (*< 1–2 months since last update*), then a plugin may be used.
Use the following as guidance:
* Discuss the implications and technical use-cases with a lead or senior developer first.
* Discuss any ongoing or fixed costs related to using or acquiring the plugin with a producer or account lead.

### Who Pays for the Plugin
The client always pays for plugins that require paid subscriptions or single-seat licenses for regular updates and support. For this reason, be sure to get approval from a producer or account lead before committing to the project plan. 

We have developer licenses for ACF5 Pro and Gravity Forms. These licenses are for internal use only, but we do provide distributions of those plugins via our base theme.

### When Not to Use a Plugin
If the plugin is not already included with the platform, has not been included in a specification, or is not part of the barrel base theme image, then you likely should not be adding a plugin. Plugins become liabilities over time, so if the functionality is small enough, it’s better to build it right into the theme. If you must use a plugin, be sure it works properly and include it as a known dependency for the project by adding warnings or notices to that effect as well as updating documentation in the theme. 

*Absolutely DO NOT* attempt to access global functions or classes exposed by a plugin. 

*If* you must do this, then you must check for its existence and not rely on its return data. 

You also have to ensure that the theme does not break when the plugin is deactivated.

*Again, do not directly use plugin functions or classes unless your functionality is decoupled from the theme and checks for the presence of the plugin and its activation status before attempting to use them.*

## Be As DRY As Possible
### Don't Repeat Yourself

- If there is something you do more than twice, write a function for it. 
- If you’re making a lot of functions that do similar things, make a single function with options. 
- If you’re making a lot of related functions and subsequent functionality, make a class with its own methods and properties. 
- Just remember to refactor when you can, and reduce your redundant code.

Separate classes can give you a set of functionalities that extend WordPress. The class in this [gist](https://gist.github.com/wturnerharris/7408110) demonstrates one for members-only functionality, encapsulating the feature much like a plugin should.

## WordPress Action and Filter Hooks

There are so many [documented](https://codex.wordpress.org/Plugin_API#Hooks.2C_Actions_and_Filters) as well as undocumented hooks and filters available in WordPress.

### Register Hooks Cleanly and Consistently
Most filters are traditionally thrown into the functions.php file. This can quickly become messy and hard to find. Break those filters into like groups of files based on the filter or functionality.

If you have a class that handles specific functionality for WordPress, then use a class-based pattern:
```php
class WP_Base_Class {
    
    function __construct () {
    	add_action( 'init', array( &$this, 'initialize' ), 10, 2 );
    }
    
    public function initialize()
    {
        // this gets called on the init hook
    }
}
new WP_Base_Class();
```

### Some useful actions and filters include:

#### AJAX
The ajax hooks allow you to create ajax functions that return data for the frontend. You can now use the [WordPress REST API](https://developer.wordpress.org/rest-api/) to register your own endpoints for plugins and themes, so the ajax hooks should only be considered for small, one-off ajax requests.

```php
    add_action('wp_ajax_get_feed', 'get_feed_ajax' );
    add_action('wp_ajax_nopriv_get_feed', 'get_feed_ajax' );
```

#### SCRIPTS
Use `wp_enqueue_scripts` action hook to register and enqueue scripts to the header or footer.

```php
    add_action( 'wp_enqueue_scripts', 'theme_scripts_enqueue' );
    add_action( 'admin_print_scripts-' . $page_hook_suffix, 'admin_scripts_enqueue');
```

## Templates, Modules, Components, and Partials

Some call them modules, some call them fragments, and some call them partials. Whatever terminology you give it, keep them organized and use the template system. It’s a good idea to know the [hierarchy](http://codex.wordpress.org/images/1/18/Template_Hierarchy.png) of templates in order to use them as much as possible over creating any custom includes or requires for template data. 

**Avoid using [get_template_part](http://codex.wordpress.org/Function_Reference/get_template_part)** and instead use an `include`, which will allow very modular code with little performance impact. 

### Custom Post Types and Taxonomies

In most cases, our practice is to define a page and/or page template for a custom post type so that we can define data for the template on a page-level basis. Only use archive templates when no data needs to be defined beyond dynamic content.

**Example Specification 1:**
- Define a template at templates/page-news.php as /* Template: News */
- Create a new page called "News" and assign it the News template.
- Define a custom "news" post type without an archive (`has_archive = false`)
- Allow "news" posts to appear as sub pages of the News page.

**Example Specification 2:**
- Define a custom field group for the News landing page in theme options.
- Define a custom "news" post type with an archive.
- Allow "news" posts to appear as sub pages of the News archive template.

## Images

Always add image sizes for the variations in which an image will appear on the frontend. Add these custom image sizes to your theme functions.

*Never display the full size image* for any image displayed on the frontend. 

Use Featured Images when possible, especially when used in multiple places, falling back to ACF Image upload fields for specific use-cases.

```php
    /**
     *
     * Add image sizes for thumbnails
     *
     * @return  void
     */
    function add_image_sizes() {
        add_image_size( 'category-thumb', 300 ); // 300 pixels wide (and unlimited height)
        add_image_size( 'homepage-thumb', 220, 180, true ); // (cropped)
    }
    add_action( 'after_setup_theme', 'add_image_sizes' );
```

## Rewrites

WordPress follows a set of predefined rewrite rules to accomplish assembling the templates. You can override these rules, but do so with caution. Generally adding rewrite rules should be a last resort when normal routes conflict with custom ones. For example: When you want your news blog to appear under a base of news/ but you don’t want the rest of the site to appear under the news/ base. You can instead add a rewrite rule to override the url for your posts. Additionally you might have a normal page at news/ which you’re using for general page content. This is a very tricky situation and can only be resolved by modifying rewrite rules. You an resort to creating a different path so that they do not conflict, but you might be left with a bunch of funky routes with similar naming conventions which could be confusing to the user.

```php
    /**
     *
     * Add rewrite rules for custom routes with no post types
     *
     * @return	void
     */
    function add_rewrite_rules() {
    	//flush_rewrite_rules(true);
    	add_rewrite_rule('resources/categories/([A-Za-z0-9-]*)?$', 'index.php?post_type=pagename=resources&resource=$matches[1]', 'top' );
    	add_rewrite_rule('resources/categories/([^/]+)/page/?([0-9]{1,})/?$', 'index.php?pagename=resources&resource=$matches[1]&paged=$matches[2]', 'top' );
    }
    add_action( 'init', 'add_rewrite_rules' );
    
    /**
     *
     * Add query variable to global in order to switch the template with custom routes
     *
     * @return	void
     */
    function add_query_vars( $query_vars ) {
    	$query_vars[] = 'resource';
    	return $query_vars;
    }
    add_filter( 'query_vars', 'add_query_vars' );
    
    /**
     *
     * Switch template if custom route detected
     *
     * @return	void
     */
    function switch_template() {
    	global $post;
    	$template = false;
    	if ($resource = get_query_var('resource')) {
    		$post = get_page_by_path("resources");
    		$template = locate_template("templates/resources.php");
    	}
    	if ($post && $template) {
    		load_template($template);
    		die;
    	}
    }
    add_action( 'template_redirect', 'switch_template' ); 
```

For another example using a class, see [this](https://gist.github.com/wturnerharris/7413478) gist.

## Performance & Security Considerations

### Profiling and Monitoring

Use New Relic regularly (included on [Pantheon sites](https://pantheon.io/docs/new-relic/) for free) to check performance bottlenecks. Activities should include reviewing the slowest processes as well as any key transaction traces. You can also set key transactions manually or via api functions in code. Remember to check for their existence before using.

If New Relic is not available, use [XDebug](https://xdebug.org/) and/or an IDE with support for a PHP Profiler like [PHPStorm](https://www.jetbrains.com/phpstorm/). 

### Using nonces

Use nonces for any custom form/post submission or when dealing with obtaining data from WordPress. 

If page caching is being employed on a server, then the nonce will likely be stale and cached from another user. In most cases, we will use page caching, so an asynchronous request for a nonce is a viable alternative. 

See the following pseudocode:

```js
 // frontend 
 get_nonce( wp_admin_ajax_nonce, set_nonce ) // ajax function to retrieve nonce from the backend
 
 set_nonce() // save nonce to local storage or cookie
 
 submit_data( wp_admin_ajax_request, request_with_nonce ) // ajax request to get data using your nonce
```

```php
 // backend
 function wp_admin_ajax_nonce( $request ) {} // set a nonce and return it
 
 function wp_admin_ajax_request ( $data ) {} // verify the nonce and proceed with sending data if the nonce is valid
```

Read more on [nonces](https://codex.wordpress.org/WordPress_Nonces). 

### Filesystem

Filesystem writes should be disabled for any production environment. In addition, disabling the ability to edit plugins, themes, etc in WordPress should be [disallowed](https://codex.wordpress.org/Hardening_WordPress#Disable_File_Editing)

### User Input

**Never trust user input**. Always use proper [escaping](https://codex.wordpress.org/Validating_Sanitizing_and_Escaping_User_Data) functions when handling user input from search queries, ajax function, form posts, etc.

### Handling the Database

Avoid direct database queries. WordPress has built-in functionality for getting information in and out of the database. Just as with any PHP application, always use prepared statements and **never trust user input**. 

#### Using $wpdb

You can’t always use built-in functionality to access the database, but you can most certainly use the `$wpdb` [abstraction layer](https://codex.wordpress.org/Class_Reference/wpdb) built into WordPress to handle all CRUD functionality. If you’re using mysql_connect(), then you're not using the correct library.

Using these functions ensures that the application is benefiting from any optimizations or caching that is already implemented within those functions. See Database Queries for tips.

## APIs

Please make use of WordPress APIs and classes. It might take some reading up, but it’s very easy to add a [settings](http://codex.wordpress.org/Settings_API) page the WordPress way using the Settings API. You can also use the existing class for [tables](https://gist.github.com/wturnerharris/7413971) by extending it in order to create a visually seamless backend.

## Preferred Plugins

If not already included, here are preferred plugins:
- Gravity Forms
- Advanced Custom Fields Pro 5
- Custom Post Type UI
- Intuitive Custom Post Order
- Kraken
- WordPres SEO (Yoast)

## Do Not Forget

* Are you adding a screenshot.png to your theme?
* Did you include a readme.md to theme root?
* Are you commenting your functions and complex code?

## Additional Resources for Best Practices

WordPress has [standard practices](http://make.wordpress.org/core/handbook/coding-standards/) for Theme developers, which includes PHP, HTML, Javascript, and CSS recommendations. These are not biblical, but they should be reviewed and closely followed for broad reception, especially when your code might be handed off to another developer.

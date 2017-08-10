### Barrel Development Best Practices

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

## Be As DRY As Possible
### Don't Repeat Yourself

- If there is something you do more than twice, write a function for it. 
- If you’re making a lot of functions that do similar things, make a single function with options. 
- If you’re making a lot of related functions and subsequent functionality, make a class with its own methods and properties. 
- Just remember to refactor when you can, and reduce your redundant code.

Separate classes can give you a set of functionalities that extend WordPress. The class in this [gist](https://gist.github.com/wturnerharris/7408110) demonstrates one for members-only functionality, encapsulating the feature much like a plugin should.

## WordPress Action and Filter Hooks

There are so many [documented](http://codex.wordpress.org/Plugin_API#Hooks.2C_Actions_and_Filters) as well as undocumented hooks and filters available in WordPress that enable you to override, filter, or change how things normally behave. If you can filter content before it is output, you can manipulate the way things appear.

    if (!has_filter('profit', 'the_profit')) {
    	add_filter('profit', 'the_profiteer');
    	apply_filters('profit', $the_content);
    }
    
    function the_profiteer ($content) {
    	return trim($content);
    }

In addition to the filter functions, there are a number of action functions that allow you to check if an action has run, 

### Some useful actions and filters include:

#### AJAX
The ajax hooks allow you to create ajax functions that return data for the frontend. You can now use the [WordPress REST API](https://developer.wordpress.org/rest-api/) to register your own endpoints for plugins and themes, so the ajax hooks should only be considered for small, one-off ajax requests.

    add_action('wp_ajax_get_feed', 'get_feed_ajax' );
    add_action('wp_ajax_nopriv_get_feed', 'get_feed_ajax' );
    
#### SCRIPTS
Use `wp_enqueue_scripts` action hook to register and enqueue scripts to the header or footer.

    add_action( 'wp_enqueue_scripts', 'theme_name_scripts' );
    add_action( 'admin_print_scripts-' . $page_hook_suffix, 'my_plugin_admin_scripts');

Use a CDN-backed jQuery script path or deregister JQuery altogether on new themes.

    function re_register_jquery(){
    	global $wp_version;
    	if ( is_admin() || in_array($GLOBALS['pagenow'], array('wp-login.php', 'wp-register.php')) ) return;
    	wp_enqueue_script( 'jquery' );
    
    	// Check to see if we're on 3.6 or newer (changed the jQuery handle)
    	$jquery_handle = ( version_compare( $wp_version, '3.6', '>=' ) ? 'jquery-core' : 'jquery');
    	$wp_jquery_ver = $GLOBALS['wp_scripts']->registered[$jquery_handle]->ver;
    	$jquery_google_url = '//ajax.googleapis.com/ajax/libs/jquery/'.$wp_jquery_ver.'/jquery.min.js';
    	wp_deregister_script( $jquery_handle );
    	wp_register_script( $jquery_handle, $jquery_google_url, '', null, true );
    }
    add_action('init', 're_register_jquery');

Similarly the following function moves all registered scripts to the footer, except jquery:

    function move_scripts_to_footer() {
        global $wp_scripts;
    	if ( is_admin() || !is_object($wp_scripts) ) return;
        foreach( $wp_scripts->queue as $handle ) :
    		$src = $wp_scripts->registered[$handle]->src;
    		$deps = $wp_scripts->registered[$handle]->deps;
    		if ( "jquery" != $handle ) {
    			wp_deregister_script($handle);
    			wp_enqueue_script($handle, $src, (count($deps)>1?$deps:''),'',true);
    		}
        endforeach;
    }
    add_action( 'wp_print_scripts', 'move_scripts_to_footer', 1);
    add_action( 'wp_footer', 'wp_print_footer_scripts', 1); // needed for above action to work

In both of the above functions, we use the little-known 5th argument, which sends the scripts to wp_foooter() in footer.php.

## Templates, Modules, Components, and Partials

Some call them modules, some call them fragments, and some call them partials. Whatever terminology you give it, keep them organized and use the template system. It’s a good idea to know the [hierarchy](http://codex.wordpress.org/images/1/18/Template_Hierarchy.png) of templates in order to use them as much as possible over creating any custom includes or requires for template data. Understand how [get_template_part](http://codex.wordpress.org/Function_Reference/get_template_part) works and use it to keep your loops, structure, and parts consistent. This would allow you to be very modular. 

The general composition of your templates should follow the template-loop-template routine. 

    <ul>
    <?php while (have_posts() ): ?>
    	<li><?php the_title(); ?></li>
    <?php endwhile; ?>
    </ul>

### Custom Post Types

With regards to the template hierarchy, it’s best to use known templates for post type landing pages when possible. If you have a ‘news’ post type, it would make sense that you have a landing page to show all news articles. If your other post types fit with the same style of output as your news, you can get away with using only the archive.php template file. You can use variations within the loop portion of the archive template should any post type deviate from the norm. Finally you could use the archive-$post_type.php template. These practices ensure there is a template for these data types.

### Images

Always use Featured Image sizes sized to pull the appropriate size image, never have the template display the full size image. Add custom image sizes to your theme functions. 

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
    
### Rewrites Beware

WordPress follows a set of predefined rewrite rules to accomplish assembling the templates. You can override these rules, but do so with caution. Generally adding rewrite rules should be a last resort when normal routes conflict with custom ones. An example might occur when you want your news blog to appear under a base of news/ but you don’t want the rest of the site to appear under the news/ base. You can instead add a rewrite rule to override the url for your posts. Additionally you might have a normal page at news/ which you’re using for general page content. This is a very tricky situation and can only be resolved by modifying rewrite rules. You an resort to creating a different path so that they do not conflict, but you might be left with a bunch of funky routes with similar naming conventions which could be confusing to the user.

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

For another example using a class, see [this](https://gist.github.com/wturnerharris/7413478) gist.

## Handling the Database

Avoid direct database queries. WordPress has built-in functionality for getting information in and out of the database. Using these functions wherever possible makes sure that you are benefiting from any optimizations or caching that is already implemented within those functions. See Database Queries for tips.

### Using $wpdb

You can’t always use built-in functionality to access the database, but you can most certainly use the abstraction layer built into WordPress to handle all CRUD functionality. If you’re using mysql_connect(), you’re just wrong!

### APIs

Please make use of WordPress APIs and classes—that’s what they’re there for. It might take some reading up, but it’s very easy to add a [settings](http://codex.wordpress.org/Settings_API) page the WordPress way using the Settings API. You can also use the existing class for [tables](https://gist.github.com/wturnerharris/7413971) by extending it in order to create a visually seamless backend.

## Keep it Clean. Stay Classy San Diego

* Are you adding a screenshot.png to your theme?
* Did you include a readme.md?
* Are you commenting your functions and complex code?

## Additional Resources for Best Practices

WordPress has [standard practices](http://make.wordpress.org/core/handbook/coding-standards/) for Theme developers, which includes PHP, HTML, Javascript, and CSS recommendations. These are not biblical, but they should be reviewed and closely followed for broad reception, especially when your code might be handed off to another developer.

### Barrel Development Best Practices

# WordPress Best Practices
 
## Useful Config During Development

It’s always a good idea to keep the backend as fresh as possible. This might mean to remove certain thresholds or keep the database pruned while in development. A few things that make things easier to spot errors and improve your workflow.

Set the frequency of the trash being emptied to zero. This effectively removes the trash functionality, and all posts, when deleted, will be gone—no trace of them in the database.

`define( 'EMPTY_TRASH_DAYS', 0 ); // Zero days`

Do not suppress error logging and output. This often helps if you are using third party plugins as a dependency. You will then see errors pop up a lot more, which give good insight as to whether the plugin code is being maintained. WordPress constantly let’s you know when certain functions are deprecated.

`define( 'WP_DEBUG', true ); // Zero days`

Disable or reduce post revisions—not autosaves; there’s another constant for that. This reduces the amount of database entries.

`define( 'WP_POST_REVISIONS', false ); // Or an integer fewer than 3`

## Use Plugins Sparingly

If you’re apt to use a plugin as a dependency to your theme, at least have a look under the hood. We often require a small portion of code from a plugin that forcing it to be installed and relying on its functionality seems ridiculous. These plugins become liabilities over time, and in order to reduce our exposure to issues with those third-party plugins, it’s better to build the functionality right into the theme. Look at the code; if it’s not too overwhelming grab a snippet and see if that will work for you. This actually goes against WordPress theme standards, which would encourage the use of plugins as they are meant to be as modular as possible. We do not make themes for WordPress, however; we make WordPress themes, or WordPress-based, applications and websites for clients. As such, we should have our clients in mind for usability, ease, and portability. If you must use a plugin, be sure it works properly and include it as a known dependency for the project. You can even add warnings or notices to that effect.

## Be As Dry As Possible

If there is something you do more than twice, write a function for it and don’t just copy and paste the same block over and over again. If you’re making a lot of functions that do similar things, make a single function with options. If you’re making a lot of related functions and subsequent functionality, make a class with its own methods and properties. This is similar to how a normal plugin would function, as separate classes giving you a set of functionalities that extend WordPress. The class in this [gist](https://gist.github.com/wturnerharris/7408110) demonstrates one for members-only functionality.

### Init Custom Classes

Use the include_once directive to include other classes you may have created. You can simply include them directly in the functions.php file or include them in an init-run action hook. The differences between require and include (and their sisters require_once and include_once) are in error verbosity alone; that is, the require directives will produce fatal errors if the file is not found whereas the includes will only produce a warning. Since we’re using classes that we might only want instantiated once during runtime, we only include_once.

    /**
     * Initializes all custom theme classes and functions
     *
     * @return    void
     */
    function init_custom_classes() {
    	// setup custom post types
    	include_once dirname(__FILE__)."/inc/custom-post-types.php";
    	// setup custom fields
    	include_once dirname(__FILE__)."/inc/custom-fields.php";
    }
    
    add_action( 'init', 'init_custom_classes', 1);

### WordPress Action and Filter Hooks

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

    add_action('wp_ajax_get_feed', 'get_feed_ajax' );
    add_action('wp_ajax_nopriv_get_feed', 'get_feed_ajax' );
    
#### SCRIPTS

    add_action( 'wp_enqueue_scripts', 'theme_name_scripts' );
    add_action( 'admin_print_scripts-' . $page_hook_suffix, 'my_plugin_admin_scripts');

WordPress highly encourages the use of their enqueues actions to process and include scripts and styles in your theme. Sometimes you would just prefer the scripts to be in the footer. The following is a hack by WordPress standards, but if you understand the routine, you may find it an acceptable workaround. This function is especially useful if another plugin is loading jquery and you want a specific version but served up from Google’s CDN. In this case, we’re enqueing jquery, comparing the version that wordpress loads by default but instead of using the local version, we’ll deregister the local and register the google CDN file of the same version. This ensures broad compatibility amongst all WordPress working parts.

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

## Templates and Partials

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

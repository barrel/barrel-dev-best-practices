### Barrel Development Best Practices

# Shopify Best Practices
----------------------

## Getting Started

###Tools###

- Shopify themes use a Ruby-based language called **liquid**, which you can read more about here: [http://docs.shopify.com/themes/liquid-documentation/basics](http://docs.shopify.com/themes/liquid-documentation/basics)
- **[Timber](https://github.com/Shopify/Timber)** and **[Skeleton](https://github.com/Shopify/skeleton-theme)** are great starter themes to begin with, and great resources in general
- Use the [Shopify theme gem](https://github.com/Shopify/shopify_theme) to work on a theme locally, with saved changes automatically pushed to the Shopify servers.
- There are some existing Barrel Shopify projects that have instructions for setting up Grunt-compiled files with the Shopify theme gem. See [Craft and Caro](https://github.com/barrel/craft-and-caro), [DailyBurn](https://github.com/barrel/DailyBurn), and [Parachute Home](https://github.com/barrel/Parachute-v2). 

### Theme Structure

Each theme has at least five directories.

- `/assets` contains any files that you want uploaded for global use in your theme (images, stylesheets, js files).
- `/config` contains files related to [Theme Settings](http://docs.shopify.com/themes/theme-development/templates/settings)
- `/layout` contains layout templates, which can be thought of as the "master templates" that individual page templates are rendered within. **theme.liquid** is the default layout file
- `/snippets` creating any **.liquid** file within this directory will allow it to be used as a global snippet
- `/templates` contains the main theme templates; more on that under the **Templates** heading below
- `/locales` is optional, containing files related to internationalization/multilanguage support 

More information on these theme files and folders can be found here: [http://docs.shopify.com/themes/theme-development/templates](http://docs.shopify.com/themes/theme-development/templates)


### Templates ###

These are required for every Shopify theme.

+ `404.liquid`
+ `article.liquid`
+ `blog.liquid`
+ `cart.liquid`
+ `collection.liquid`
+ `index.liquid`
+ `list-collections.liquid`
+ `page.liquid`
+ `product.liquid`
+ `search.liquid`

For stores with customer accounts enabled, the customer account templates are: 

+ `customers/account.liquid`
+ `customers/activate_account.liquid`
+ `customers/addresses.liquid`
+ `customers/login.liquid`
+ `customers/order.liquid`
+ `customers/register.liquid`
+ `customers/reset_password.liquid`


### Shopify JS assets

- The most important Shopify-hosted js asset to include in any theme is **option_selection.js**, which contains a bunch of methods related to displaying product prices correctly (e.g. if the price changes based on selected variants, if a product is sold out, if the currency on the front-end changes). 
- To include it, use the **shopify_asset_url** filter as so:

```
{{ 'option_selection.js' | shopify_asset_url | script_tag }}
```

- Including this on your site will allow you to access a variety of objects, such as the product, its currently-selected variant, and all related attributes. 

- A **selectCallback** method is called whenever a product is loaded on a page. It checks to see what variant of that product is currently selected, and changes the display accordingly. It is usually called in **product.liquid** and/or a snippet of some kind. More on this method can be found here: [http://docs.shopify.com/support/your-website/themes/can-i-make-my-theme-use-products-with-multiple-options](http://docs.shopify.com/support/your-website/themes/can-i-make-my-theme-use-products-with-multiple-options)

```
   $(function() {
     var selectCallback = function(variant, selector) {
       $product = $('#product-' + selector.product.id);
 		
       if (variant && variant.available == true) {
         ...
       } else {
         ...
       }
     };
   });
```


- The **shopify_common.js** and **customer_area.js** files contain a few scripts related to the customer accounts area of your Shopify theme (e.g. province and country dropdowns for the Address section). They are usually included in the header like so:

```
{{ 'shopify_common.js' | shopify_asset_url | script_tag }}

{% if template contains 'customers' %}  
  {{ 'customer_area.js'  | shopify_asset_url | script_tag }}
{% endif %}
```


- The [Shopify AJAX API](http://docs.shopify.com/support/your-website/themes/can-i-use-ajax-api) can be used by including the **api.jquery.js** file in your theme. The methods in that file are primarily related to AJAX-updating the cart (adding, removing, updating products).

```
{{ 'api.jquery.js' | shopify_asset_url | script_tag }}
```

###Uploaded Assets###
- When linking to uploaded assets, make sure to use Shopify's asset syntax (which links to the Shopify CDN-hosted version of the file):

```
{{ 'icons.png' | asset_url }}
{{ 'init.js' | asset_url | script_tag }}
{{ 'style.css' | asset_url | stylesheet_tag }}
```
**Be careful of CDN-hosted font-face files -- the CDN files have ?123 versioning added, which doesn't always play well with fonts.*

- If you're using any liquid code in your js or css files -- useful when implementing user Theme Settings -- you'll need to append **.liquid** to the file name. For example, in **style.scss.liquid**:

```
body.index {
    background: {{ settings.body_bg_color }}; 
    color: {{ settings.text_color }}; 
}

```

### Styling
- Shopify automatically compiles **.scss** files, so use that to your advantage!

###Checkout Pages###

- Because Shopify's checkout pages are hosted elsewhere (**checkout.shopify.com**), content on that page is not editable. It can only be styled (or rather, re-styled) by reading a theme's **checkout.css** file (**checkout.scss** if the store has "Responsive Checkout" enabled). 
- In this file, you'll need to override the default checkout styles with ones closer to your theme's appearance.
- You can also add the **.liquid** extension to this file (e.g. for calling Theme Settings values).

----------------------

##Tips


###Template Conditionals###

- Class or ID your body tag based on the template shown (**template-product, template-collection, template-page**, etc.)
- Use template conditionals to change the page title and SEO/OG tags in the theme header:

```
{% if template contains "product" %}
	<meta property="og:title" content="{{ product.title }}" />
	<meta property="og:type" content="product" />
	<meta property="og:url" content="{{ shop.url }}{{ product.url }}" />
	<meta property="og:image" content="{{ product.featured_image | product_img_url: 'original' }}" />
{% else %}
	<meta property="og:title" content="{{ shop.name }}" />
	<meta property="og:url" content="{{ shop.url }}" />
	<meta property="og:image" content="{{ 'logo.png' | asset_url }}" />
{% endif %}
	
```


###Recycle Product "add to cart" code###

- If you anticipate having multiple add to cart buttons on a page (e.g. a "Quick View" or "Quick Add" button on the collections, related products, recently viewed, or search templates), you'll want your add to cart elements to use classes, not IDs. 
- You'll also probably want to dump this code in a snippet of some kind. 


###Displaying Product Prices###

- Wrap any instance of your shop's price in some kind of classed tag -- chances are that its style or value will change based on product variant options, sale prices, or other factors.
- Don't forget all types of price displays (sale prices with "compare at" values, prices with ranges based on variants)

###Site Search###

- In some cases, you'll want to limit search results to products by adding a hidden field to your search form (thus hiding pages, blog posts, and other content from showing up):

```
<form method="get" action="/search">
	<input type="hidden" name="type" value="product"/>
	<input type="text" name="q" placeholder=" Type to search" class="search_query" /> 
	<button type="submit">Submit</button>
</form>
```

##Good To Know

###Redirects###

Shopify has an interface for 301 Redirects, in the **Navigation > URL Redirects** section of the admin dashboard (**URL Redirects** button is in the top right).

### Snippets ###

When including snippets such as 

```
{{ include 'snippet-name' }}
```

...you can add one value in your include:

```
{{ include 'snippet-name' with 'test' }}
```

...In the file that contains the snippet, **snippet-name** is now the name of a variable, with the value **test**. 

###Ajax API versus Shopify API###

*The Shopify API is different than the Ajax API!*

The **[Ajax API](https://docs.shopify.com/support/your-website/themes/can-i-use-ajax-api)** returns JSON-encoded responses for **product** and **cart** information, allowing you to add items to your cart and fetch product info via javascript.  

The **[Shopify API](https://docs.shopify.com/api)** provides a ton of endpoints for many components of your store, including Orders, Customers, Collections, Pages, Products, Metafields, and more. (See the API Reference column on the left of the [documentation page](https://docs.shopify.com/api)).

You can quickly access this JSON information in your browser if you are logged into your store, by visiting your store URL (**using https**) with the endpoint location specified in the docs (e.g. http://www.parachutehome.com/admin/collections.json)

To *create, edit, or update* this information, you'll need to build a separate app. To *GET* most of this information, you may also need a separate app, but...

### ...I don't know how to categorize this ###

If you're logged into your store and go to:

+ store-name.myshopify.com/products.json
+ store-name.myshopify.com/collections.json
+ store-name.myshopify.com/pages.json

...you'll get a response, which is *different* from the response you'll get if you go to **/admin/collections.json** (for example). I can't explain why, but regardless, you can submit GET requests to the URLs above -- handy for "related products" or similar features. 

- - - - -

## Internal demo stores, and stores for testing

To access these stores, you can log in through [shopify.com/partners](http://www.shopify.com/partners) (creds in 1pass) or find out who might have a username and password. 

### Stores for testing (these should be pw-protected) ###

+ [barrel-dev-shop.myshopify.com](http://barrel-dev-shop.myshopify.com) (general sandbox)
+ [sugar-in-tea.myshopify.com](http://sugar-in-tea.myshopify.com) (Seasons theme tester)
+ [langosh-group3762.myshopify.com](http://langosh-group3762.myshopify.com) (Weekend theme tester)


### Demo stores (do not test on the published themes in these stores, please!) ###

+ **Seasons themes**
  - [Spring Jewelry](http://barrel-jewelry.myshopify.com)
  - [Fall Chocolates](http://barrel-chocolate.myshopify.com)
  - [Winter Beauty](http://barrel-beauty.myshopify.com)
+ **Weekend themes**
  - [Outdoor Apparel](http://outdoor-apparel.myshopify.com/)
  - [Sunday Coffee](http://barrel-coffee.myshopify.com/)
  - [Bow Wow Ties](http://pet-accessories-3.myshopify.com/)
  
### Creating a Demo store

Login at [shopify.com/partners](http://www.shopify.com/partners), go to **Development Stores**, and click **Create a new development store** in the right-hand corner. 


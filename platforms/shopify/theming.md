# Shopify Best Practices

### 📚 Table of Contents
**1. Basics**
  - Liquid
  - Barrel CLI
  - Base Theme

**2. Building a Theme**
  - Structure
  - Templates
  - Snippets (Modules/Components)
  - Sections

**3. Guidelines**
  - Global
  - Structure a Snippet
  - Paremeters
  - Inclusion
  - SVG icons
  - Tutorials
  - Variables

**4. Workflow**
  - Environments
  - Shopify Apps

* * *

### 📍 1. Basics
#### Liquid
Liquid is Shopify's ruby based language that is used to build templates with the help of a set of objects, tags and filters.
A more thorough documentation can be find [here](https://help.shopify.com/themes/liquid/basics) but just as an introduction:
  - **Objects** are used to display content stored in the database of your store, like `product`, `collection` or `customer`. Each of those object has attributes that can be output using `{{` `}}` that way:

  ```
  This {{- product.title -}}} belongs to the {{- collection.title -}} and costs $ {{- product.price -}}.
  ```

  - **Tags** are used for programming logic and help to tell a template what to do. They are wrapped in `{%` `%}` and can be as basic as a `if`/`else` statement or can be a bit more developed like a `capture` or `assign` statement:

  ```
  {%- if product.title contains 'exceptional' -%}
    This product is exceptional.
  {%- else -%}
    This product is basic.
  {%- endif -%}
  ```

  - **Filters** are methods that are applied to something that is being output like a string, a number, an object or a variable. They are therefore used inside `{{` `}}` and are seperated by a pipe `|`:
 
  ```
  How much does this {{- product.title | capitalize -}} cost? It cost {{- product.title | money_with_currency -}}
  ```

#### Barrel CLI
[Barrel CLI](https://github.com/barrel/barrel-cli) is a NPM package that we use heavily in theme development. 
- Actions that can be performed:
	- Build CSS and javascript assets using Webpack, Babel, Postcss
  - Live reloading in the browser using HMR
	- Deploy themes to Shopify
- Configuration for theme kit can be set up in the `config.yml` file located in the `/dist` or root directory of the project.

#### Base Theme
TO DO

* * *

### 📍 2. Building a Theme
#### Structure
Those are the 7 required directories for a Shopify theme:
- `/assets` - *Contains all images, stylesheets and scripts.*
- `/config` - *Contains all the global [theme settings](https://help.shopify.com/themes/development/theme-editor/settings-schema).*
- `/layout` - *Contains the "master" template `theme.liquid`. All templates are rendered inside this one.*
- `/locales` - *Contains the translation files for the content on the store.*
- `/modules` - *Contains most of the theme code, including snippets, sections and templates where applicable* 
- `/snippets` - *Contains bits of liquid code that don't really fit into the modules directory*
- `/templates` - *Contains all the templates.*

#### Templates
- Those are the common templates required for a Shopify theme:
	- `404` - *Used for all pages with an invalid url.*
	- `article` - *Used for all blog posts.*
	- `blog` - *Used for all blog pages.*
	- `cart`- *Used for the cart page.*
	- `collection` - *Used for all collections pages.*
	- `index` - *Used for the homepage.*
	- `list-collections` - *Used for the collection list page.*
	- `page` - *Used for all basic pages.*
	- `product` - *Used for all product pages.*
	- `search` - *Used for the search results page.*
	- `gift_card` - *Used for the gift card page communicated to a customer after purchase.*
- Those are the customer account templates required for a Shopify theme:
	- `customers/account` - *Used for the customer account homepage.*
	- `customers/activate_account` - *Used for the activate account form page.*
	- `customers/addresses`- *Used for the customer addresses list page.*
	- `customers/login` - *Used for the login form page.*
	- `customers/order` - *Used for the customer orders history page.*
	- `customers/register` - *Used for the register form page.*
	- `customers/reset_password` - *Used for the reset password form page.*
- Alertnative templates can be created for `article`, `collection`, `page` and `search` and have to be named `name_of_template.new_name.liquid` (for example: `page.custom.liquid`).

#### Snippets (Module/Components)
- Snippets are chunk of reusable codes that can be included anywhere by using the `include` tag:

```
{%- include 'my_snippet' -%}
```

- We distinguish 2 main types of snippets: Modules and Components.
- Components are small chunk of codes without any particular "logic" that can be used as a "tile" to build a module, like: a `button`, an `image` or a `product-item`.
- Modules are more elebatorated chunk of codes like: a `hero`, a `slideshow` or a `product-grid`.
- Use parameters to pass content and properties to a snippet:

```
{%- include 'button', style:'white', value:'Add to Cart' -%}
```

- Use `with` to define a snippet:

```
{%- include 'button' with 'link' -%}

// Inside the snippet

{%- case button -%}
	{%- when 'link' -%}
		<a class="btn">
			// Snippet content
		</a>
	{%- default -%}
		<button class="btn">
			// Snippet
		</button>
{%- endcase -%}
```

#### Sections
Sections are similar to Modules as they are elaborated chunk of codes, but their one property that set them appart is that they can have dynamic content created and manipulated within them via their `block` settings feature.
- Sections are added using the `section` tag:

```
{%- section `hero` -%}
```

- Settings for a section are added inside the section file within the `{%- schema -%}` `{%- endschema -%}` tag:

```
{%- schema -%}
  {
    "name": "Hero",
    "settings": [ {
        "type": "text",
        "id": "title",
        "label": "Title"
    } ]
  }
{%- endschema -%}
```

- A section can be **static** or **dynamic**:
	- Dynamic sections can be added multiple times as independent instances and reordered on the homepage.
	- Static sections can be added multiple times accross all templates but they would all be considered as one same instance with common settings.
- Blocks can be added within a limit range or infinitely to a section.
- Blocks can have a type, and for each type a set of different settings. For example, a `slideshow` section has 2 block types: `Background Image Slide` and `Default Slide`:

```
{%- schema -%}
  {
    "name": "Hero",
    "settings": [], 
    "blocks": [ {
        "type": "background-image",
        "name": "Background Image Slide",
        "settings": [ {
            "id": "background_image",
            "type": "image_picker",
            "label": "Background Image"
        },{
            "type": "text",
            "id": "title",
            "label": "Title"
        } ]
    }, {
        "type": "default",
        "name": "Default Slide",
        "settings": [ {
            "id": "background_color",
            "type": "color",
            "label": "Background Color"
        }, {
            "type": "text",
            "id": "title",
            "label": "Title"
        } ]
    } ]
  }
{%- endschema -%}
```

- Variables created within a section are not available outside of it.
- More informations [here](https://help.shopify.com/themes/development/theme-editor/sections).
- Additional good read for content management flexibility with Sections [here](https://medium.com/@maxrolon/content-management-flexibility-4aabc56aabf2).

* * *

### 📍 3. Guidelines
#### Global
- Don't use regular liquid brackets like this `{%- ... -%}` and `{{- .. -}}` but make sure to use ones that include hyphens like this `{%- ... -%}` and `{{- ... -}}`. Adding hyphens to liquid brackets remove all unecessary white space around your liquid declaration.

For example:
```
<span class="word">
  {{- my_word -}}
</span>
```
will render HTML like this:
```
<span class="word">
  Hello!
</span>
```
Whereas using liquid brackets with hyphens like this:
```
<span class="word">
  {{- my_word -}}
</span>
```
will render HTML like this:
```
<span class="word">Hello!</span>
```

- For all JS asset files use the `script_tag` filter and for all Stylesheet asset files use the `stylesheet_tag` filter:

```
{{- 'styles.css.liquid' | asset_url | stylesheet_tag -}}
{{- 'main.js' | asset_url | script_tag -}}
```

- For all image assets use the size parameters:

> Set width only

```
{{- product.image | img_url: '400x' -}} or {{- 'my-image.jpg' | asset_img_url: '400x' -}}
```

> Set height only

```
{{- product.image | img_url: 'x400' -}} or {{- 'my-image.jpg' | asset_img_url: '400x' -}}
```

- Use the `split` filter to convert a string to an array:

```
{%- assign colors = 'blue,orange,red,purple,green,yellow' | split: ',' -%}

{%- for color in colors -%}
  {{- color -}}
{%- endfor -%}
```

- Convert a string to a number using a math filter like `minus`:

```
{%- assign string_number = '5000' -%}
{%- assign number = string_number | minus: 0 -%}
```

- Use `canonical_url` in `theme.liquid` to handle pages associated with no templates (commonly happens when apps renders page on the store).

For example, a store locator app renders a map to locate the brand stores on the current Shopify store. This app uses no template and is render in `theme.liquid` where `{{- content_for_layout -}}` is located. If you need to render specific modules where the store locator app renders the map - A quick workaround is to use the `canonical_url`:

Store Locator URL:
`my_store.myshopify.com/apps/store-locator`

Inside `theme.liquid`:
```
...
{%- if canonical_url contains 'store-locator' -%}
  {%- include 'store-locator-hero' -%}
  // Where the app will renders its content.
  {{- content_for_layout -}}
{%- else -%}
  {{- content_for_layout -}}
{%- endif -%}
..
```

#### Structure a Snippet
- Snippets that are used in multiple templates (Modules like `hero` or `slideshow` - Components like `button` or `image`) shouldn't have any hard coded content - only variables whose values are inherited from the snippet parameters. All snippets variables should have a default value:

```
{%- include 'hero', title: my_title, content: my_content -%}

// Inside the 'hero' snippet

{%- assign title = title | default: false -%}
{%- assign content = content | default: false -%}

<section class="hero">
  {%- if title -%}
    <h1 class="hero__title">{{- title -}}</h1>
  {%- endif -%}
  
  {%- if content -%}
    <p class="hero__content">{{- content -}}</p>
  {%- endif -%}
</section>
```

- All variables declared in a snippet (Module/Component) are not "isolated" within it, so always clear them:

```
{%- assign title = 'value' | default: false -%}
{%- assign index = 5 | default: 0 -%}

// Snippet Content

{%- assign title = nil -%}
{%- assign index = nil -%}
```

#### Paremeters
- When a snippet has more than 2 paremeters return to the line.

Do this:

```
{%- include 'hero',
  title: my_title,
  content: my_content,
  image: my_image
  cta_text: my_cta_text -%}
```

Don't do this:

```
{%- include 'hero', title: my_title, content: my_content, image: my_image, cta_text: my_cta_text -%}
```

#### Inclusion
Prioritize *components inside modules*, *modules inside sections* and *sections inside templates* (or *modules inside templates*). Try to prevent to have too much inline code when possible.

> How a layout should be structured?

```
<head>
  <title>{{- page_title -}}</title>
  {%- include ‘meta-tags’ -%}
  {%- include ‘stylesheets’ -%}
</head>

<body>
  <main class=”main”>
     {%- include ‘header’ -%}

     {{- content_for_layout -}}

     {%- include ‘footer’ -%}
  </main>

  {%- include ‘libraries’ -%}

  {%- if template contains ‘collection’ -%}
    {%- include ‘optinmonster-collection’ -%}
  {%- endif -%}
</body>
```

> How a template should be structured?

```
{%- section 'hero' -%}

{%- include ‘product-3-up’ -%}

{%- include ‘carousel’ -%}

{%- include ‘newsletter-block’ -%}
```

> How a section should be structured?

```
{%- assign section = section.settings -%}

// Multiple sections use the same hero layout, instead of adding inline code, the 'hero' module is called with the section settings passed to it as parameters.
{%- include ‘hero’, title:section[‘title’], content:section[‘content’] -%}

{%- schema -%}
   // Section settings
{%- endschema -%}
```

> How a module should be structured?

```
{%- assign title = title | default: false -%}
{%- assign content = content | default: false -%}
{%- assign btn_text = btn_text | default: false -%}

<section class=”hero”>
  {%- if title -%}
    <h1 class=”hero__title”>{{- title -}}</h1>
  {%- endif -%}

  {%- if content -%}
  	<p class=”hero__content”>{{- content -}}</p>
  {%- endif -%}
  
  // Include a component.
  {%- if btn_text -%}
    {%- include ‘button’, value: btn_text -%}
  {%- endif -%}
</section>

{%- assign title = nil -%}
{%- assign content = nil -%}
{%- assign btn_text = nil -%}
```

#### SVG Icons
- Create a `icon` liquid file in `/snippets/components`:
- Structure the file as follow:

```
{%- case icon -%}
  {%- when 'cart' -%}
     // Add inline SVG here.
  {%- when 'search' -%}
     // Add inline SVG here.
  {%- when 'arrow' -%}
     // Add inline SVG here.
{%- endcase -%}
```

- Include every icon as follow:

```
{%- include 'icon' with 'cart -%}
```

#### Tutorials
- [Product Exploded View](https://kb.barrelny.com/shopify-development-product-exploded-view/)
- [Split Product Content](https://kb.barrelny.com/shopify-development-split-content/)

#### Variables
- Variables should be named with an underscore e.g. "variable_name" rather than camel case e.g. "variableName"
- Liquid variables should be scoped to the module in which they are used. In most cases, you should not be declaring a variable in one file and using it inside of another.
- Each variable should be reset to `nil` at the bottom of the file it's being used in.

Do this (in one file):
```
{%- assign variable_name = "value" | Default: blank -%}
// do some stuff in your template file
{%- assign variable_name = nil -%}
``` 

Don't do this: 

`file_1.liquid`

```
{%- assign variable_name = "value" | Default: blank -%}

{%- include 'snippet_name' -%}
```

`snippet_name.liquid`

```
<div class="{{- variable_name -}}"></div>

{%- assign variable_name = nil -%}
```
* * *

### 📍 4. Workflow
#### Environments
- All environments are declared in the `config.yml` file:

```
  theme_id: 
  api_key: 
  password: 
  store:
```

Use the --env flag to specify which environment should be updated:

```
theme watch --env=ENVIRONMENT
```

> Development

```
- This environment targets any theme on the development store.
- This environment is use for the development of the project.
``` 

> Staging

```
- This environment targets the unpublished theme on the production store that will be used for the next release.
- This environment should always be up to date with the development environment.
- JIRA Tickets Review and QA should be done on this environment before release.
```

> Production

```
- This environment targets the published theme on the production store.
- Updates to this environment should only be made for hotfixes.
```

#### Shopify apps
Those are basic Shopify apps that should be use in most of the themes:
- [Superfields](https://support.maestrooo.com/category/109-superfields)
- [Product Reviews](https://help.shopify.com/manual/apps/apps-by-shopify/product-reviews)

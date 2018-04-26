# Barrel Base Themes

Barrel primarily works with three platforms. Both WordPress and Shopify use themes to customize websites and both have and use content management systems. The below sections will detail the tech stack, notable platform contraints, and what's included in each theme. 

## Shopify

**Demo:** https://base-dev.myshopify.com/
**Issues:** https://gitlab.com/barrel/barrel-shopify/issues

**Tech Stack:**

1. [WebPack](https://webpack.js.org/) - helps bundle and process sass and javascript files.
2. [Sass](https://www.shopify.com/partners/blog/a-beginners-guide-to-building-shopify-themes-with-sass-part-1-getting-started-with-sass/) - default CSS preprocessor for Shopify. 
3. [ES6+ JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript), or ECMAScript 2015, is a standard that Barrel uses to build custom javascript powering the frontend interactions. Barrel does not actively use frameworks such as jQuery or React, but we can easily plug and play with those frameworks depending on the project, timeline, and goals.
4. [Liquid](https://help.shopify.com/themes/liquid/basics) - default template language for Shopify.
5. [Barrel CLI](https://github.com/barrel/barrel-cli) - a command-line tool that bundles much of the dependencies required to allow a robust local development workflow, including [automated] deployments.

## WordPress

**Demo:** http://dev-barrel-base-theme.pantheonsite.io/
**Issues:** https://gitlab.com/barrel/barrel-wordpress/issues

**Tech Stack:**

1. [WebPack](https://webpack.js.org/) - helps bundle and process sass and javascript files.
2. [Sass](https://sass-lang.com) - default CSS preprocessor used at Barrel. 
3. [ES6+ JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript), or ECMAScript 2015, is a standard that Barrel uses to build custom javascript powering the frontend interactions. Barrel does not actively use frameworks such as jQuery or React, but we can easily plug and play with those frameworks depending on the project, timeline, and goals.
4. [PHP7](https://pantheon.io/blog/php-7-now-available-all-sites-pantheon) - the underlying backend language powering WordPress.
5. [Barrel CLI](https://github.com/barrel/barrel-cli) - a command-line tool that bundles much of the dependencies required to allow a robust local development workflow, including [automated] deployments.
6. [WordPress](https://wordpress.org/) - the powerful and flexible software that powers the WordPress experience.

## Static

**Demo:** https://barrel-assemble.netlify.com/
**Issues:** https://gitlab.com/barrel/barrel-assemble/issues

**Tech Stack:**

1. [Browserify](http://browserify.org/) - helps bundle and process sass and javascript files. We are upgrading this to WebPack.
2. [Sass](https://sass-lang.com) - default CSS preprocessor used at Barrel. 
3. [Handlebars](https://handlebarsjs.com/) - template language similar to Mustache or Liquid.
4. [Node](https://nodejs.org/en/) - a command-line tool that allows a robust local development workflow.
5. [Netlify](http://netlify.com/) - a platform that hosts static content, has multiple environments, and auto-deployments via git.
6. [Assemble](https://github.com/assemble/assemble) - allows developers to freely build the frontend markup in a modular fashion that mirrors our processes for WordPress and Shopify. 
7. [JSON DataLayer](https://www.json.org/) - since there is no CMS, most content needs to be hardcoded, but we use a JSON dataLayer to contain content that will likely change. This makes editing content much easier and keeps content separate from the build process that includes the HTML, CSS, and JavaScript. 

## Goals and Road Map

We want our base themes to serve not only as starting point for developers but as a visual and interactive training tool. This can be best described as a starting point for designers, producers, new developers, and more to better understand the content types and settings available to these systems, the limitations, the apps and/or plugins that sit alongside the platforms, and any pre-fabricated modules that can be re-used project to project and across platforms. Currently our base themes only have the structural boilerplate code.

If you see an issue that repeatedly gets missed (404, GTM/GA Tag fields, third-party services), please feel free to add an issue to the respective project. You can also submit an [issue](https://gitlab.com/barrel/barrel-dev-best-practices/issues) to this repository if you are curious about or could not find a best practice.
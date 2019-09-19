# Assemble Best Practices

## Background

Assemble is a static [site generator](https://davidwalsh.name/introduction-static-site-generators) that we use to create basic landing pages—think: marketing, coming soon, basic pages, etc). Assemble is super robust as it provides a framework for dealing with templates and partials and injecting content via data files or APIs. This means that a website or blog could be made static like most any traditionally dynamic CMS like WordPress, Drupal, et al. Once run, the templates, assets, and data files are "assembled" into static html files and directories that can be hosted mostly anywhere. The biggest benefit is that we can keep a modular structure for DRYest code, but the load times will typically be 10x faster since no database or backend will be needed to fetch content or data.

### How We Use Assemble

Assemble was selected for its flexibility and unopinionated nature. For that reason we can use liquid templates using the liquidjs engine, we can maintain static json data files for content, and our typical frontend tech stack fits nicely without any conflicts.

We also use Assemble for our base modules—a standard library of frontend components that follow typical design patterns. If you used it on two or more projects, then it should probably be a base module.

You can find Barrel's [Assemble](https://gitlab.com/barrel/barrel-assemble) build on GitLab, and it's as easy as pulling down and running `npm i && npm run build` to produce a fully-compiled—erm assembled—static html website that looks like you used a traditional CMS to build and manage.

### Can We Use Something Else?

Of course you can! The core principle of using a static site generator is for speed of development and speed of frontend performance, so another static site generator will likely do just the same. You just might not have access to liquid template rendering nor would you be able to easily leverage our existing base modules, which all port over nicely to Shopify. There's a plethora of [other site generators](https://www.staticgen.com/) out there.

## Best Practices

The greatest thing about our Assemble build is that you can follow our [liquid](https://gitlab.com/barrel/barrel-dev-best-practices/blob/master/platforms/shopify/theming.md#liquid) templating best practices as well as our [CSS](https://gitlab.com/barrel/barrel-dev-best-practices/blob/master/languages/css.md) and [JS](https://gitlab.com/barrel/barrel-dev-best-practices/blob/master/languages/javascript.md) best practices for the frontend.
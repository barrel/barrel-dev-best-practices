# Handlebars Best Practices

Handlebars is a logic-less templating engine that dynamically generates your HTML page. Handlebars templates look like regular HTML, with embedded handlebars expressions.

### 📚 Table of Contents
**1. Global**
  - Expressions declaration
  - Barrel CLI
  - Base Theme

**2. Structure**
  - Layout
  - Templates
  - Partials

**3. Helpers**
**4. Working with Context**

* * *

### 📍 1. Global
#### Expressions declaration
A handlebars expression is a `{{`, some contents, followed by a `}}`. There should always be a space after/before the opening/closing brackets:

```
{{ title }}
```

Some cases where you won't have a space after the brackets will be when the expression is used to call on helpers or when including partials.

*Helpers*
```
{{#if title '' }}
  There's no title here
{{/if}}
```

*Partials*
```
{{> header }}
```

* * *

### 📍 2. Layout
Used as the "skeleton" of the page, the layout should not contain any inline HTML except for the basic structure of the page. Meaning only `partials` should be included or `template markups` for the templates content.

```
<!DOCTYPE html>
<html>
<head>
  // Here you can include inline HTML.
</head>
<body>
  // Only template markups and partials.
</body>
</html>
```

* * *

### 📍 3. Templates
Inline HTML shoudl be limited as much as possible inside templates. Instead prioritize the inclusion of partials.

```
{{> hero-banner }}
{{> plp-grid }}
{{> related-products }}
```

* * *

### 📍 4. Partials
- Partials shouldn't have any hardcoded text as we want to think of those as modules. Therefore they should be easily re-usable with different content using parameters:

*Inside a Template*
```
{{> hero-banner 
  title='This is my title' 
  text='This is the text that will go below my title' 
  image='img/src/image_1.jpg' }}
```

- Parameters inside a partial should always use single quotes `'` and not double quotes `"`.

* * *

### 📍 5. Helpers

* * *

### 📍 6. Working with Context




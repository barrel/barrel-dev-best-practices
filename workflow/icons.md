## Icon Best Practices

### Table of Contents
1. [Icon Approach (SVGs)](h#icon-approach-svgs)
2. [Preparing SVGs](#preparing-svgs)
3. [Using SVGs inside your project](#using-svgs-inside-your-project)

## Icon Approach (SVGs)

If you need to use icons in a project going forward we recommend using an [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG)-based workflow, as described below.

## Preparing SVGs

The steps below provide some guidance on how to prepare SVGs for insertion into your project. The workflow below is dependant on being provided with Sketch files and having access to Illustrator. Usually, these steps will be executed by the designer on the project. 

1. Open Sketch file
1. Export icon as .svg from Sketch (Steps 3-10 apply when saving an SVG from an icon created in Adobe Illustrator)
1. Open .svg in Adobe Illustrator and ungroup all objects
1. Expand any strokes into outlines
1. Merge shapes that overlap/connect with one another
1. Cutout any areas from shape that are meant to be transparent
1. Make all paths compound paths
1. Size your artboard to fit the artwork
1. Copy your .svg and paste it into a new Illustrator file the same dimensions as your previous artboard
1. Save your file as an .svg – using the default settings from Illustrator

### Using SVGs inside your project

> Note: This method is only suitable if your project needs to support browsers IE11 an up, as the ```use``` attribute is a modern standard. We're working on a JS polyfill to provide ```use``` support for older browsers.

### Methodology

We include SVGs inside a project using a modular infrastructure. This method has a few ingredients:
- The main SVG file that includes fully valid svg and paths for each icon
- The helper that is used to collect the svg reference to the master SVG
- The include that utilizes the helper to output the correct icon in the DOM

#### Shopify Method

`icon-paths.liquid`:
```html
<!-- icon-facebook -->
<svg width="1792" height="1792" viewBox="0 0 1792 1792" xmlns="http://www.w3.org/2000/svg">
  <path id="icon-facebook" fill="currentColor" d="M1343 12v264h-157q-86 0-116 36t-30 108v189h293l-39 296h-254v759h-306v-759h-255v-296h255v-218q0-186 104-288.5t277-102.5q147 0 228 12z"/>
</svg>
<!-- Additional icons defined here -->
<svg xmlns="http://www.w3.org/2000/svg">
  <path id="icon-different" fill="currentColor" d=""/>
</svg>
```

Below is an example in liquid (Shopify) but the same methodology can be used in Wordpress or a static website (using Assemble or otherwise).

`icon.liquid`:
```liquid
{% case icon %}
  {% when 'facebook' %}
    <svg viewBox="0 0 1792 1792">
      <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#icon-facebook" x="0" y="0"></use>
    </svg>
  {% comment %}Add all your SVGs here in different cases{% endcomment %}
{% endcase %}
```

In your DOM, include `icon.liquid` and pass in the variable that determines the icon to include.

```liquid
<div class='facebook-icon'>
  {% include 'icon' with 'facebook' %}
</div>
```

#### WordPress Method
WordPress can largely mirror this workflow. 

**TBD**

## Notes
- There can be only one `id` attribute with the same value defined in the entire HTML document.

### Questions
- Can any of the above be automated with a local "task"?
- Can any of the above be streamlined for use across any project?
- Are there any limitations to this approach? 
- Why not use the font-based approach?


## How to add icons to a project

### Table of Contents
1. [Icon Approach](#icon-approach)
2. [Preparing SVGs](#preparing-svgs)
3. [Using SVGs inside your project](#using-svgs-inside-your-project)

## Icon Approach

If you need to use icons in a project, in most cases we recommend using an [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG)-based workflow, as described below. However, there are many caveats to using svg files. Please ensure all implications for using them are considered.

## Preparing SVGs

Follow [these steps](/howtos/how-to-prepare-svg-files.md) for guidance on how to prepare SVGs before being used on the project. Below are some additional formatting considerations.

1. Avoid `id="*"` attributes within the svg file, especially if the file is to be *output* multiple times on a single page; i.e. using a search icon in the menu and in the search bar or submit buttons using iconography in several places on the page such as search submission, newsletter submission, etc. *The exception to this rule* is if you only output one svg with multiple svgs in a single file.
2. Avoid using `<title/>` and `<desc/>` elements within the SVG file, and instead use adjacent elements with visually hidden text to give context; i.e. `<button aria-label="Subscribe to Newsletter"><svg/><span class="hidden-text">Subscribe to Newsletter</span>

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

## Caveats
- There can be only one `id` attribute with the same value defined in the entire HTML document, so this includes svg files.
- Any `<?xml` declarations at the top of the svg file should be stripped to keep the HTML document valid. This is only required for svgs that are directly output in html.

### Questions
- Can any of the above be automated with a local "task"?
- Can any of the above be streamlined for use across any project?
- Are there any limitations to this approach? 
- Why not use the font-based approach?


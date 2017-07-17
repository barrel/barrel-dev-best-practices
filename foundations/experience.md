### Barrel Development Best Practices

# User Experience
 
## DOM Visibility

### Make sure the "most-important" page features are available as soon as possible.
- Plan page loading "steps" in-line with UX priorities (what loads first, what is lazy-loaded or delayed).
- Delay the load of blocking JavaScript and CSS that isn't essential for displaying the highest priority content.
- Use asynchronous scripts whenever possible.
- Content should be formatted to be readable as a document in the case of no CSS or JavaScript (position primary content such as posts or articles before sidebars or secondary information).

### Don't rely soley on JavaScript for essential functionalities.
- Content should be visible.
- Essential images should have a fallback.
- Links should still work (avoid inline onClick, use anchors in links and on page, AJAX links should point to resource).

## Image Loading

### Standard Loading

Add vanilla JavaScript loading functions and CSS responsible for loaders inline in the head, so it's available right away. Always put images in a wrapper div (allows for loading animations, forcing aspect ratios). For example:

```css
.bg-wrapper {
 width: 100%;
 height: 100%;
 position: absolute;
 top: 0;
 opacity: 0;
 transition: opacity 200ms;
}
.bg-wrapper.loaded,
.no-js .bg-wrapper {
 opacity: 1;
}
.bg-wrapper img{
 display: none;
 position: absolute;
}
```
```javascript
function imgLoaded(img) {
	var imgWrapper = img.parentNode;
	imgWrapper.style.backgroundImage = 'url(' + img.src + ')';
	imgWrapper.className += imgWrapper.className ? ' loaded' : 'loaded';
}
```
```html
<div class="bg-wrapper" style="background-image: url(http://foobar.jpg);">
 <img src="http://foobar.jpg" onload="imgLoaded(this)" />
</div>
```

The above function assumes modernizr is being used. The code waits for images to load prior to applying them as a background image to the containing div. If javascript is not enabled, the background image is applied as it normally would be. Consider using the onError event for user uploaded images.

### Lazy Loading

Lazy load using JavaScript to throttle number of images loaded at once. Waypoints can be used to determine when the loading script is fired for certain images on the page.

- Load from data-src and or src-set (also can be used for serving different image sizes based off media-width).
- Provide fallback to display images when JavaScript is not available. Consider using a `<noscript></noscript>` tag for loading images that would otherwise be lazy-loaded.

For example:

```javascript
$('[data-src]').each(function(){
	var src = $(this).attr('data-src') || '',
	img = new Image(),
	el = this;
	
	img.onload = function(){
		if (el.tagName == 'DIV'){
			el.style.backgroundImage = 'url(' + src + ')';
		} else if (el.tagName == 'IMG'){
			if (!! el.parent){
				el.parent.replaceChild(img, el);
			} else { 
				el.src = src;
			}
		} 
		setTimeout(function(){
			$(el).addClass('visible');
		}, 300);
	}
	img.src = '' + src;
});
```

## Image Sizes

*Elaborate on size benefits. Best practices for shrinking images and goal image sizes! (Under 200k? Why?)*

- Use jpeg for photographs.
- Use pngs and/or svg for sprites (keep on a single sheet, SVG id helps make this possible).
- Force WP to serve appropriate sizes, never original.

The below example alters compression/quality levels of uploaded jpegs and pngs:

Refer to WordPress file and image handling functions.

```php
<?php

/**
 * Handle any uploads from within wordpress
 */
add_filter( 'wp_handle_upload_prefilter', function($file){
	if ( empty($file['tmp_name']) ) return $file;
	$source = $file['tmp_name'];
	list($image_width, $image_height, $image_type) = getimagesize($source);
	$mime_type = image_type_to_mime_type($image_type);
	
	// create the file
	switch($image_type) {
	    case 2:
			$source = imagecreatefromjpeg($image); 
			imagejpeg($source,$file['tmp_name'],80);
			break;
	    case 3:
			$source = imagecreatefrompng($image); 
			imagepng($new_image,$image_path,8); 
			break;
  	}
	
	$source = imagecreatefromjpeg($file['tmp_name']);
	imagejpeg($source,$file['tmp_name'],80);
	return $file;
});
```

## History

- Listen to state change to trigger changes rather than triggering changes with click. This keeps track of changes with back/forward buttons.
- Use History.js polyfill to support differences in browser implementations (and optionally add support to older browsers)

```js

// var nativeHistorySupport = (typeof window.history !== 'undefined');

// Check for history
if(History.enabled) {

	// Add event listener to statechange event
	$(window).bind('statechange', function(event) {
		var currentState = History.getState();
		
		// currentState.data = {
		// 	id: 'tab1'
		// }
		
		// Update the DOM to reflect the new state.
		
		// e.g., scroll to linked section
		$(window).scrollTop($('#'+currentState.data.id).offset().top);
	})

	// Prevent default click action and push new state
	$('a.ajax-link').on('click', function(event) {
		// Allow new tab clicks to function normally
		if(event.metaKey || event.which != 1) {
			return;
		}
		
		event.preventDefault();
	
		var $a = $(this);
	
		var data = {
			id: 'tab1'
		};
		var url = $a.attr('href');
		var title = $a.attr('title');
	
		History.pushState(data, url, title);
	});

}

```
## Animations
See the [css](https://github.com/barrel/barrel-dev-best-practices/blob/master/css.md#animation-more) section.

## First Hop (Assets)

*Elaborate and explain the reasons behind each of these. Examples should be shown. Call out expensive JS libraries.*

- Check final minified sizes.
- Set a loading / performance budget. TBD
- Keep under 64kb (when possible). ???
- Use webfont loader script to help manage font loading even with local fonts.
- Shrink fonts by removing unnecessary character sets.
- For icon fonts like FontAwesome, pull only the glyphs you need, create new icon font.
- Look at the size of your vendor scripts and use the smallest possible version.

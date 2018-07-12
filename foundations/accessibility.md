### 📚 Table of Contents
- Modals
- Automatically Changing Content
- Alt Tags
- Aria Attributes
- Field Labels
- Element Focus
- Tab Indexing
- Possible Issues
- Definitions
- Testing Directions
- Resources
 
***
#### 📍 Modals

Any fixed, [non-critical](https://docs.google.com/document/d/1XUlG2O_zFGfBOgCt6VyqSG3pFR7U-SX1oenDIcjqQnU/edit?ts=5b47a1d6#heading=h.hibncrbkavxx) element in a page should be included at the bottom of the document. Consider including a 'modals' module  in your footer template where you can logically instantiate the modals necessary for each page. There are two main benefits to this practice:
1.  Performance
1. Rendering non-critical markup at the bottom of the page will help to ensure that all of the initial markup rendered by the page is relevant to [above the fold content](https://docs.google.com/document/d/1XUlG2O_zFGfBOgCt6VyqSG3pFR7U-SX1oenDIcjqQnU/edit?ts=5b47a1d6#heading=h.dcmtd3s6wdze).
1. Accessibility
1.  If modals are included towards the top of the page (or even the module they're in) screen readers will process their content as if it were immediately visible on the page (undesired).

***
#### 📍 Automatically Changing Content

Auto-scrolling sliders, for example, will cause ADA issues as it can be difficult for users with disabilities to understand why content is changing on the site.

**Notes:**
- We need to test and establish some principles on handling large DOM transformations since they are inevitable in a lot of situations.

***
#### 📍 Alt Tags

Alt attributes are what screen-readers use to describe a website's non-decorative element (often an `<img>` tag). So, for any element requiring an alt attribute, the attribute should be provided as a description of either what that element is or what a user can do with that element.
`<img>` tags should always have alt attribute, even if empty.

The following elements should have alt attributes:
-   Links
-   Buttons
-   Images

Don't do this:
```html 
<img src="pancakes.jpg" alt="pancakes" />
```

Do this:
```html
<img src="pancakes.jpg" alt="Stack of pancakes covered in syrup" />
```

For a non-image example, if you have a button (or similar element) that shows the display of a slide-out menu, your markup could look something like this:
```html
<div class="btn js-show-menu" role="button" alt="Button to view the website's main menu">Menu</div>
````

When an image is decorative (meaning the information provided by the image might already be given using adjacent text, or the image is intended to make the website more visually attractive) use an empty alt attribute. If an empty alt attribute is not used some screen readers will announce the file name of the image (not a pleasant experience).

Don't do this:

```html
<img src="jan-huang.jpg" alt="Jan Huang" />
<h1>Jan Huang</h1>
```

Don't do this:

```html
<img src="jan-huang.jpg" />
<h1>Jan Huang</h1>
```

Do this:

```html
<img src="jan-huang.jpg" alt="" />
<h1>Jan Huang</h1>
```

Do not populate "image of" or "photo of" in the alt text, this information is already provided by the screen reader.

Don't do this:

```html
<img src="jan-huang.jpg" alt="Photo of Jan Huang" />
<h1>Employee of the month</h1>
```

Do this:

```html
<img src="jan-huang.jpg" alt="Jan Huang is a fictitious freelance developer for Barrel" />
<h1>Employee of the month</h1>
```

***
#### 📍 Aria Attributes

It's important to add aria-label, role, and/or aria-labeledby attributes to describe what an element's purpose is. This issue was often thrown on HTML5 tags (specifically `<section>`) that are pretty ambiguous. This is pretty similar to Alt Tagging. [Documentation](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques)  

**Notes:**
- We need to add more detail here -- what's the difference between aria and alt?

***
#### 📍 Field Labels

Form fields need to have `<label>` tags to describe what information is expected. The label also needs to be visible, so we can't get away with having the markup there and hiding it in CSS. The proper solve in a lot of our design cases would be to position:absolute the labels and use them as we do the `placeholder` attribute, then move them out of the way or hide them with javascript. Google's forms are a good example. 

**Notes:**
- We need to add more detail here.

***
#### 📍 Element Focus

Removing the :focus property from an element will throw accessibility issues on SiteImprove.

***
#### 📍 Tab Indexing

Most work will be to ensure the content flows naturally so that tab indexing works without additional development. This works relatively smoothly across browsers, except Safari.

Tab indexing natively follows your markup order. As such, your markup should be written in a way that reflects desktop designs first and then reflow to mobile designs from there (mobile users aren't tabbing the site).

***
#### 📍 Possible Issues

What project aspects do we currently have (or anticipate having) issues with?
-   Standardizing heading tags (h1, h2, etc.)

***
#### 📍 Definitions

##### Non-Critical

An element or that does not immediately contribute to the user's experience upon loading the page. Many page-speed analytics tools will classify any content that's not immediately above the fold as non-critical.

###### Examples & Notes

1.  Content lightboxes
1.  newsletter signup that pops up after 3 seconds
1.  video lightbox
1.  Menu drawers may be a bit of a case-by-case basis, as you'll need these for screen-reader purposes.

##### Above The Fold Content

Any content that is immediately visible within the browser's viewport when the page is initially loaded.

*** 
#### 📍 Testing Directions
How to make Safari on a Mac accessible:
1.  Testing on a Mac: System Preferences -> Keyboard -> ShortCuts (tab) -> Full Keyboard Access -> All Controls
1.  For Tabbing to work on Safari: Preferences -> Advanced -> Press tab to highlight each item on a page (check this)

***
#### 📍 Resources

##### Articles
<http://alistapart.com/article/my-accessibility-journey-what-ive-learned-so-far>

##### Courses
<https://www.udacity.com/course/web-accessibility--ud891>

##### Documentation
<https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques>
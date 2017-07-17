# Goals
1. Maintainability
2. Strict syntax
   - allows for linting
   - self documenting
   - more readable
3. Flexibility
   - encourages reuse
   - context agnostic styles
3. Speed 
   - performant selectors
4. Well commented

## TOC
1. [Code Structure](#structure)
2. [Workflow](#workflow)
3. [Selectors](#selectors)
4. [Naming](#naming)
5. [Naming Convention](#naming)
6. [Nesting](#nesting)
7. [Breakpoints](#breakpoints)
8. [Variables](#variables)
9. [Mixins & Extends](#mixins-and-extends)
10. [Stateful Classes](#stateful-classes)
11. [Javascript Hooks](#javascript)
12. [Misc](#misc)

### Base Rules
1. Use good selector intent. Be proactive and methodical.
2. Write everything to be reusable and location independant.
3. Don't nest if you can help it.
4. Keep selectors as short as possible.
5. Comment as you go.

The rest of this doc will explain how to do the above ;)

### Useful Definitions
_**Base Properties**_
Properties declared within a class that are low-level, meaning they are shared among many classes and not usually subject to change (but not always). Examples are `display`, `position`, etc.

_**Implementation Properties**_
Properties declared within a class that are specific to the context of the element that receives the class. These are things that are not easily shared due to their often unique values, or necessity of change (like whitespace between breakpoints). Examples include `margin`, `padding`, `::after/::before`, etc.

### Structure
```bash
base/
  |– settings
  |– colors
  |- whitespace
  |– grid
  |– etc
elements/
  |– forms
  |– buttons
  |– etc
components/
  |– caption
  |– responsive-image
  |– slideshow
  |– etc
modules/
  |– hero
  |– main-slideshow
  |– etc
templates/
  |– about
  |– etc
```
The main goal here is starting from the most base-level styles and working our way up (down, in this list) in specificity. Think about it in terms of what can be reused. Grid classes can be used on elements like form fields, which in turn can be used within small components, which in turn can be composed into larger modules, which in turn make up pages and templates.

### Workflow
- use `.scss` dialect
  - or `.css`, \*gasp\*
- Autoprefixer
- minify for production

### Selectors
Always use **classes.** IDs shouldn't have a place in your CSS. Since their very nature is one of *non-reusability*, they work against the natural reuse possible with CSS and the cascade.

Don't think about pieces as being unique. They hardly every need to be.

Another thing to talk about here is *selector intent*. Selector intent is the process of deciding what needs to be styles and how you plan to go about it. Selector intent implies that you are narrow in your focus when crafting your selectors. For example, when crafting your main menu, you shouldn't do this:
```scss
header {
  ul {
    li {
      //styles  
    }  
  }  
}
```
Now you're styling any `li` within a `ul` that is also within a `header`, which also means the ability to reuse this code is nil. If you're aiming to style menu items, then be specific and concise.
```scss
// header
.site-nav {
  // styles  
}

// li
.menu-item {
  // styles
}
```
And if you can't add classes (like in Wordpress menus, where it's pretty difficult), you can still have selector intent:
```scss
.main-nav {
  // ONLY main menu items
  > li {
    // styles  
  } 

  // ONLY submenu items
  > li ul > li {
    // styles  
  }
}
```

### Naming
Barrel uses [BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/). The accronym stands for classifications of selectors: block, elements, and modifiers. You already use these, but may not have thought about them as distinct. BEM just aims to codify them into a more readable, transparent convention.

#### B is for Block
Blocks are the root element of your module or component. Elements that make up that component are then scoped to the root using BEM syntax.

Examples of blocks:
```css
.hero {
  ... 
}
.page-header {
  ...  
}
```

**Do:**
- keep the block selector short, and no more than two words

**Don't:**
- use IDs, shouldn't need them


#### E is for Element
Elements are children of Blocks. They are usually the smallest parts of a Block scoped piece of DOM/CSS, and are preferential to selecting elements by HTML tag. Be careful not to confuse elements with other Blocks. BEM selectors should usually never be "deeper" than two levels i.e. a block followed by an element.

Examples of elements:
```css
.hero__bg-overlay {
  ...  
}
.page-header__category {
  ...  
}

/* you probably don't need to do this */
.hero__background__overlay {
  ...  
}
```

**Do:**
- use CSS selectors instead of defining classes based on HTML elements
- try to keep selectors to only two "levels" deep
  - however, the syntax allows for it, so use with discretion, they get annoying to type out

#### M is for Modifier
Modifiers are the variants of a Block or Element within a Block. They can change any attribute on the selector they're applied to, but use caution when using Modifiers as a descender class. It can get tricky depending on where the definitions are located within your partial structure. Keep all modifiers of a class within it's parent definition.

Examples of modifiers:
```css
.hero__bg-overlay--white {
  ...  
}
.page-header__category--underline {
  ...  
}
```

Modifiers can be tricky as selectors, especially when the Modifier class is applied in a different partial than where it was defined. In the example below, if the dev were to change the class from `.hero__background--light` to `.hero__background--white`, the usage within `buttons.scss` would break, and it may not be immediately obvious why.
```css
/* hero.scss */
.hero__background--light {
  ...
  .hero__title {
     color: white; 
  }
}

/* buttons.scss */
.button {
  ...
  .hero__background--light & {
    background-color: white;  
  }
}

/* this is preferable, in this case */
.hero__background--light {
  .button {
    background-color: white;  
  }  
}
```

### Nesting
Nesting should be kept to a minimum. "As much as is necessary, but as litle as possible" is a good rule of thumb. It creates (oftentimes) unecessary specificity, which leads to the need to override styles. 

For example:
```html
<!-- hero.hbs -->
<div class="hero">
  <button class="button"></button>
</div>

<!-- contact.hbs -->
<div class="hero">
  <button class="download-button button"></button>
</div>
```
```css
/* hero.scss */
.hero {
  .button {
    margin-top: 10px;  
  }  
}

/* contact.scss */
.download-button {
  margin-top: 0; // won't work  
}
```

The problem is exacerbated when you don't follow a syntax like BEM. For example:
```css
/* about.scss */
.about-page {
  .image {
    ...implementation specific styles here...  
  }
}

/* hero.scss */
.hero {
  .image {
    ...implementation specific styles here...  
  }
}
```
In the above, if `.hero` lives within `.about-page`, `.hero .image` will inherit the same styles as those from `.about-page`. This *compounds* the properties, and makes it harder to maintain. A better alternative would be to use a Modifier class (along with BEM, of course), or separate the *base* styles from the *implementation specific* and group them into a class, which can be applied to both elements.

BEM syntax implies that elements live within their parent Block. **However,** this does not mean you should nest BEM selectors. One of the biggest benefits of this system is decreased reliance on external dependencies (other classes that influence styles). If you nest namespaced classes, the readability benefit you typically see with nesting is diminished, and is replaced by contextual styling issues (dependencies).
```css
/* don't do this */
.hero {
  .hero__button {
    ... styles ...  
  }  
}

/* do this */
.hero {
  ...
}
.hero__button {
  ...  
}
```

### Breakpoints
Ideally, breakpoints should be [device agnostic](http://trentwalton.com/2014/03/10/device-agnostic/), meaning they should be defined based on the content, not devices, and you [should use EMs](http://blog.cloudfour.com/the-ems-have-it-proportional-media-queries-ftw/) where possible. With SASS it's easy to convert [pixels to EMs.](https://github.com/estrattonbailey/svbstrate/blob/master/src/base/_breakpoints.scss)

Media blocks should be written with `min-width` based media queries whenever possible. Avoid mixing of `min-width` and `max-width`, since specificity and readability is easily complicated when thinking in both spaces.

Avoid writing *all* styles for either desktop or mobile, then writing your opening `@media (min-width ...` followed by *all* styles for that breakpoint. Instead, declare media blocks for your classes *within their context.* This helps with readability, because you can more easily reference responsive values for the context you're working within, often without even scrolling your text editor window. In a 500 line partial, having to scroll to the bottom to find responsive styles is a time-suck and makes writing new styles difficult for those not familiar with the code.

Don't do this:
```css
// mobile styles
.header {
  // styles 
}
.header__links {
  // styles  
}
.header__link {
  // styles  
}

// tablet styles
@media (min-width: break(874px)){
  .header {
    // styles 
  }
  .header__links {
    // styles  
  }
  .header__link {
    // styles  
  }
}

// desktop styles
@media (min-width: break(1200px)){
  .header {
    // styles 
  }
  .header__links {
    // styles  
  }
  .header__link {
    // styles  
  }
}
```

We're writing SASS for a reason: so we can cheat. Media queries are much simpler written like this:
```css
.header {
  // mobile styles
  @media (min-width: break(874px)){
    // tablet styles
  }
  @media (min-width: break(1200px)){
    // desktop styles
  }
}
// etc
```

### Variables
Variables should be used as a singular reference to a value that appears often in your CSS, much like how you might configure classes to use only one or a few values and then reuse the class throughout your markup. In this sense, variables are to be used as values, and not as configuration, as you might with a framework. Using a collection of variables to control the appearance of elements and components is generally an unecessary abstraction for a site not built with a defined interface i.e. Bootstrap.  

Basically, think of variables as controlling *properties* instead of components, and be mindful of what properties are being affected within the CSS you write, as opposed to relying on configuration to handle the complexity.

**Naming:**
SASS variables support BEM, and it helps to add context to the variable you're using. Otherwise, use all lowercase, `_` (underscore) separated variable names.

**Where to put variables:**
TODO
1. In a single partial, or in the partials they are used within?
2. Naming convention

### Mixins & Extends
Both are powerful, but can be very expensive. Ground rules:
- never extend a class within a nested selector structure
- never extend a class that contains another `@extend`
- never extend a class that contains *implementation specific* properties
- never include a mixin that contains its own selectors
- be careful when you include a mixin that contains *implementation specific* properties

**In general:**
Mixins duplicate code, so be careful what you're duplicating. Extends *hoist* selectors, so pay attention how long you selectors get, and understand what causes them. If you don't understand Mixins or Extends and their possible side-effects, it's probably best to avoid using them.

**But:**
Utility mixins like for gradients, etc, are a different beast entirely and are extremely helpful in certain situations. For instance, if Autoprefixer is not available (like on a Shopify build), a mixin can be used to prefix transitions and transforms.

### Stateful Classes
Introduced by SMACSS, stateful classes tell a developer that a component is in a current *state*, or that a script is acting upon the component. Stateful classes *are not global*, meaning they do not carry styles on their own. Scope stateful classes to an existing selector using "active" prefixes `.is-`, `.has-`, `.was-`, etc. 

```css
.info__tab {
  color: gray;
  &.is-active {
    color: white; 
  }
  &.has-content {
    ...  
  }
}
```

### Javascript
Never select an element in javascript by a standard class, especially one that has properties defined on it. Always use `.js-` prefixes so that other developers know what classes are being used in CSS, and which are used in JS.

### Animation ([more](http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/))
* Use transitions for simple changes from one style to another. Use keyframe animations for more complex scripted actions.
* Modern browsers can animate four things really cheaply: position, scale, rotation and opacity. If you animate anything else, it’s at your own risk, and the chances are you’re not going to hit a silky smooth 60fps.
* Use the "Continuous Page Repainting" Tool in Chrome to monitor complex or large quantities of animated elements.
* Avoid overusing `translateZ(0)` or `translate3d(0,0,0)` in a single page thereby creating too many composite layers.
* In Blink and WebKit browsers a new layer is created for any element which has a CSS transition or animation on opacity. Therefore, excessive opacity transitions could result in the same issue of forcing a separate composite layer.
* For best performance, animate CSS transform properties instead of position, margin, height/width, etc. whenever possible.

### Misc
- don't use `*` selectors, they're slow and can be dangerous if applied incorrectly
- Instead of relying on `!important`, use CSS specificity to override styles when necessary. BEM should remove any need for this, but in the event it comes up:
```css
/* page-title.scss */
.page-title {
  .button {
    color: blue;  
  }  
}

/* don't do this */
.hero__button {
  color: black !important;  
}

/* do this */
.hero {
  .hero__button {
    color: black;  
  }  
}
```

## How to images to a project

Challenge:
To create a standardized way of treating images across a project that:
- Lazy loads images when they are in the viewport
- Allows a developer to use the srcset attribute
- Supports both object-fit: cover and object-fit: contain
- Displays a loader until the image is ready to be shown
- Fades the image in when it has been loaded
- Shims object-fit for browsers that do not support it
- Includes noscript markup for browsers that do not have javascript enabled

```html
<div class="image">
  <img class="image__el obf-cover" data-normal="{{ src }}" data-srcset="{{ srcset }}" alt="{{ alt }}" />
  <i class="image__loader loader"></i>
  <noscript>
    <img src="{{ src }}" srcset="{{ srcset }}"  alt="{{ alt }}" />
  </script>
</div>
```

```css
.image {
  position: relative;
}

.image__el {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  opacity: 0;
  transition: opacity var(--fast) var(--ease);
  z-index; 2;

  .is-ready & {
    opacity: 1;
  }
}

.image__loader {
  z-index: 1;  
}

.obj-contain {
  object-fit: contain;
}

.obj-cover {
  object-fit: cover;
}

```javascript
import Layzr from 'layzr.js'

export const images = Layzr()

let objectFit = false
for (let prop in document.documentElement.style) {
  if (/object(?:-f|F)it$/.test(prop)) {
    objectFit = true
    break
  }
}

images.on( 'src:before', img => {
  let fallback, normal
  let parent = img.parentNode

  if ( img.nodeName.toLowerCase() !== 'img' ) {
    return
  }
    
  img.onload = () => {
    parent.classList.add('is-ready')
    clearTimeout( fallback )
  }
    
  if ( normal = img.getAttribute( 'data-normal' ) ) {
    fallback = setTimeout( () => {
      img.src = normal
      parent.classList.add('is-ready')
    }, 4000 )
  }
})

images.on( 'src:after', img => {
  let parent = img.parentNode

  if ( objectFit || (!/obf-/.test( img.className ) ) ) {
    return
  }

  let treatment = img.className.match( /obf-(\D[^ ]+)/ )
  if ( treatment && treatment[1] ) {
    treatment = treatment[1]
  }

  const sources =
    ( img.getAttribute( 'srcset' ) || img.getAttribute( 'src' ) )
    .split( /\s?,\s?/ )
    .reduce( ( o, val ) => {
      let brk = val.split( /\s|w$/ )
      o[ brk[1] || 0 ] = brk[0]
      return o
    }, {} )

  const src = arr => arr[ Math.max( ...Object.keys( arr ) ) ]

  parent.style.background = `url(${src})`
  parent.style.backgroundSize = treatment
  parent.style.backgroundRepeat = 'no-repeat'
  parent.style.backgroundPosition = 'center center'
  parent.classList.add('is-ready')
  img.style.opacity = 0
})

const fn = () => {
  images.update().check().handlers(true)
}

export default fn
```
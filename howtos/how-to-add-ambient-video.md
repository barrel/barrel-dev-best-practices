## How to add an ambient video to a project

Challenge:
To generate markup, styles and logic for an ambient video module that:
- Takes up 100% of the parent container
- Always fills parent container, cropping to center
- Starts playing when the element is within the viewport
- Stops playing when the element is outside the viewport
- Automatically plays on viewports above a certain width
- Shows a fallback image on viewports below a certain width
- Shows an image until the video is ready to autoplay
- Fades in when the video is ready to autoplay

```html
<div class="video">
  <video muted loop class="video__el js-video" data-src="{{ video_url }}">
  <!-- Look at the 'how to add images' howto for this image element -->
  <img class="video__img image js-image" data-normal="{{ img_url }}">
</div>
```

```css
.video {
  position: relative;
}

.video__el {
  min-width: 100%;
  min-height: 100%;
  width: auto;
  height: auto;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%) translateY(-50%);
  z-index: 2;
  display: none;
  transition: opacity var(--slow) var(--ease);

  @media( --md-viewport ) {
    &:after{
      content: 'video';
      visibility: hidden;
    }

    display: block;
  }

  .js-enabled & {
    opacity: 0;
  }

  &.is-ready {
    opacity: 1;
  }
}

.video__img {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1; 
  object-fit: cover;
}
```

```javascript
import Video from 'video-in-out.js'
import select from 'dom-select'

const videoSupport = el => {
  const css = getComputedStyle( el, ':after' ).content
  return ~content.indexOf('video')
}

const init = () => {
  select.all( '.js-video' ).map( load )
}

const load = video => {
  if ( !videoSupport() ) {
    return
  }

  Video( video, {
    readyClass: 'is-ready',
    parentEl: video.parentNode,
    autoload: true
  } )
}

module.exports = () => ( init(), init )
```
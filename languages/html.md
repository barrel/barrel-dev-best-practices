### Barrel Development Best Practices

  

# HTML Best Practices

  

## Semantics and Readability

Be as semantic as possible when structuring the markup of a page. Semantic markup means that tags are chosen based on the type of content and their hierarchy. These are documents and as such should read like a document when the presentation layer is stripped away. Semantic markup also makes it easier to implment accessibility standards when required.

  

## Understanding the HTML Specification

Understand that the HTML specification evolves. Use valid html5 elements, referring to the [HTML5 spec](https://www.w3.org/TR/html52/) provided by the W3C and ensuring broadest [browser compatibility](caniuse.com/) frequently.

  

## Validate it!

Be sure to run all processed and final html through a validator to ensure clean and valid html5 markup. Use the [w3c validator](http://validator.w3.org/) to check your output either by direct copy and paste input (for local development) or by passing the url and any httpauth information.

  

## Attributes

When there are many attributes on a single html element, it can be hard to read. To make it easier to digest, try sticking with the following conventions:

1. Keep the first attribute inline with the tag open.

2. Keep consistent tab or space indentation

3. Keep the right closing carat of the opening element tag inline with the last attribute.

  

## Images


When you work with responsive images you should take care about the image size constraints to deliver the best image suited for the specific device

[Responsive Images With Art Direction](https://www.smashingmagazine.com/2016/09/responsive-images-in-wordpress-with-art-direction/)

In general the best way to avoid loading images twice is to use the new `<source>` tag like this. (see below)

```markup
<picture>
    <source media="(min-width: 768px)" srcset="hero.jpg 1400w,
        hero-300x204.jpg 300w, hero-768x522.jpg 768w, 
        hero-1024x696.jpg 1024w" 
        sizes="(max-width: 1400px) 100vw, 1400px">
    <img srcset="hero-767x460.jpg 767w, hero-560x336.jpg 560w, 
        hero-360x216.jpg 360w" 
        alt="Front page hero alt text"
        sizes="(max-width: 767px) 100vw, 767px">
</picture>
```
  
  ### Steps
1. Wrap your required images in a `<picture>` tag
2. Specify your image constraints srcset in each source
3. Provide an image fallback after the each `<source>` per required device or viewport size

## Video

  
Use the video element to load, decode, and play video on your site.

Produce video in multiple formats to cover a range of mobile platforms.

Size videos correctly; ensure they don't overflow their containers.

Accessibility matters; add the track element as a child of the video element.

  
  [Detailed video advices from Google Developers](https://developers.google.com/web/fundamentals/media/video?hl=en)

  

### General considerations

#### Export final video at high quality

When exporting the final video from your video editing application, be sure to export it with high quality settings. Conversion and optimization for use on the web will be done later (details below).


### Using video as a background

#### Keep the video relatively short

Keep the video around 10-15 seconds in length  

#### Remove the audio channel

  
Audio can be distracting in this context, plus, background videos will not autoplay on mobile if they contain sound, so be sure to remove the audio channel completely.

**Important notes**

Autoplay function has some policies or limitations to work properly, so if you can provide this feature follow this rules.

1. Mute your video
2. Make your video relatively short
3. Don't try to remove the muted attribute through JS becuse the video can be stoped indefinitely until the user reload the webpage
4. Use the play() promise to show the video controls to the user if autoplay is disabled

[Webkit reference](https://webkit.org/blog/7734/auto-play-policy-changes-for-macos/) 

#### Provide multiple optimized formats

In order for the video to be cross browser compatible, you'll need to convert it to multiple formats, currently, WebM and MP4. To do this, I highly recommend using [ffmpeg](https://www.ffmpeg.org/) via the command line. If you have [homebrew](https://brew.sh/) it’s easy to install with `brew install ffmpeg --with-libvpx`. Commands look something like this:

```
ffmpeg -i input.mp4 -c:v libvpx-vp9 -an output.webm
ffmpeg -i input.mp4 -c:v libx264 -an output.mp4
```

-   The  `-i`  option specifies the input video.
-   The  `-c:v`  option specifies the codec to use, libvpx-vp9 for WebM, and libx264 for H.264/MPEG-4.
-   The  `-an`  option removes the audio track, just in case it wasn’t already done in the source video.

For background videos probably you won't need to keep the video in HD format so you can use standard HD (1280x720) and will have enough quality to be used as a background.

```
ffmpeg -i input.mp4 -c:v libvpx-vp9 -an -s hd720 output.webm
ffmpeg -i input.mp4 -c:v libx264 -an -s hd720 output.mp4
```

**Note** 

If you are on a Mac you can use [Handbrake](https://handbrake.fr/) to conveniently convert your videos without a terminal cli commands

#### Focus on playback performance

In order to ensure efficient streaming and playback, you'll need to optimize the bit rate. Add the  `-b:v`(target bit rate) option to the above commands
```
ffmpeg -i input.mp4 -c:v libvpx-vp9 -an -b:v 1000k -s hd720 output-1000k.webm
ffmpeg -i input.mp4 -c:v libx264 -an -b:v 1000k -s hd720 output-1000k.mp4
```

Try different bitrate settings in the conversion commands above.

-   Lower bitrates will produce smaller files and will stream better, but will have a lower quality.
-   Higher bitrates will produce larger files and may have issues streaming, but will produce higher quality.
-   A good target is between **700kb/s - 1200kb/s**.
-   Keep the bitrate as low as possible, until the quality degradation becomes noticeable and unacceptable, then increase it a little bit until it looks acceptable.

#### Consider mobile users bandwidth

Since videos are typically large files, they will consume more bandwidth and will slow down overall page load time. Consider using media queries to serve a video with smaller dimensions and a lower bitrate to small-screen devices. This will allow for more efficient streaming and playback on small-screen devices, and will consumer less overall bandwidth for those on a cellular connection.

Run the above ffmpeg command, but adjust the  `-s`  (size) option and lower the  `-b:v`  (target bit rate) option:

```
ffmpeg -i input.mp4 -c:v libvpx-vp9 -an -b:v 500k -s nhd output-500k.webm
ffmpeg -i input.mp4 -c:v libx264 -an -b:v 500k -s nhd output-500k-500k.mp4
```

The  [nhd](https://en.wikipedia.org/wiki/Graphics_display_resolution#640_%C3%97_360_(nHD))  value is another  [size preset](https://ffmpeg.org/ffmpeg-utils.html#Video-size)  which yields a frame size of 640x360, exactly one quarter of a standard HD (720) frame.

Another option would be to use media queries to serve a static image to small-screen devices. An image will consume much less cellular data than a background video.

[More Information...](https://www.viget.com/articles/best-practices-for-background-videos/)

### Modal best practices

Sometimes you will need to open a video in a modal, please consider the following best practices to do it the right way.

1. Use only a single modal to open every video in the page and replace the modal content trough AJAX or JavaScript.
2. If you want to provide different videos for mobile and desktop you should hide and pause the unecessary video in the current viewport
  
### Cross-browser considerations

* Be aware of autoplay policies in each browser
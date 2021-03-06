# Respio
Pixel perfect responsive images on every device.
THX to the Google cache シ

  - Retina ready
  - Pixelated placeholder
  - Lazyloading
  - Works with images and background images

### Version
2.3.0

### Demo
  - [Pixel perfect responsive images](http://dev.janwagner-design.de/respio/demo_respio_image.html)
  - [Pixel perfect background images](http://dev.janwagner-design.de/respio/demo_respio_background_image.html)

### Requires

* [jQuery]

## Pixel perfect responsive images
#### 1. Markup
```sh
<img data-respio-img="http://dev.janwagner-design.de/respio/image.jpg">
```
We need to remove the src attribute of your image and replace it by a new custom data attribute to prevent it from loading. Your images should have a max-width of 100% and should also be wider than your content/container.
#### 2. Google Proxy Magic (THX to @coolaj86)
```sh
function googleProxy(width, refresh, url) {
    return 'https://images1-focus-opensocial.googleusercontent.com/gadgets/proxy'
    + '?container=focus'
    + '&refresh=' + refresh
    + '&resize_w=' + width
    + '&url=' + url
    ;
}
```
#### 3. Script
```sh

$(window).on('load', respio);
$(window).on('scroll', respio);

function googleProxy(width, refresh, url) {
    return 'https://images1-focus-opensocial.googleusercontent.com/gadgets/proxy'
    + '?container=focus'
    + '&refresh=' + refresh
    + '&resize_w=' + width
    + '&url=' + url
    ;
}

function respio() {

    var windowBottom = $(window).scrollTop() + $(window).height();

    $.each($('img[data-respio-src]'), function(i, img) {
        var $img = $(img);

        if($img.attr('src', '')) {
            var thumbUrl = googleProxy(10, 604800, $img.data('respio-src'));
            $img.attr('src', thumbUrl);
        }

        setTimeout(function() {
            var imgTop = $img.offset().top;

            if(imgTop >= windowBottom * 1.5) { return; }

            var devicePixelRatio = (window.devicePixelRatio > 1) ? window.devicePixelRatio : 1;
            var imgWidth = $img.parent().width() * devicePixelRatio;

            var originUrl = $img.data('respio-src');
            var originImgUrl = googleProxy(imgWidth, 604800, originUrl);
            var $originImg = $('<img/>').hide().appendTo($('body'));

            var isLoaded = false;
            var isResized = false;

            var originImg = $originImg.get(0);
            originImg.onload = function() { isLoaded = true; };
            originImg.src = originImgUrl;

            var waitInterval = setInterval(function() {
                if (isLoaded) {
                    clearInterval(waitInterval);
                    $img.attr('src', originImgUrl);
                    $originImg.remove();
                    $img.removeAttr('data-respio-src');
                    $img.css('height', '');
                }
            }, 0);
        }, 0);
    });

}
```
## Pixel perfect background images
#### 1. Markup (width and height required)
```sh
<div data-respio-bg="http://dev.janwagner-design.de/respio/image.jpg" width="xxx" height="xxx"></div>
```
#### 2. Script
```sh
$(window).on('load', respio);
$(window).on('scroll', respio);

function googleProxy(width, refresh, url) {
    return 'https://images1-focus-opensocial.googleusercontent.com/gadgets/proxy'
    + '?container=focus'
    + '&refresh=' + refresh
    + '&resize_w=' + width
    + '&url=' + url
    ;
}

function respio() {

    var windowBottom = $(window).scrollTop() + $(window).height();

    $.each($('[data-respio-bg]'), function(i, img) {
        var $img = $(img);

        var thumbUrl = googleProxy(10, 604800, $img.data('respio-bg'));
        $img.css('background-image', 'url(' + thumbUrl + ')');

        setTimeout(function() {
            var imgTop = $img.offset().top;

            if(imgTop >= windowBottom * 1.5) { return; }

            var devicePixelRatio = (window.devicePixelRatio > 1) ? window.devicePixelRatio : 1;
            var imgWidth = $img.parent().width() * devicePixelRatio;

            var originUrl = $img.data('respio-bg');
            var originImgUrl = googleProxy(imgWidth, 604800, originUrl);
            var $originImg = $('<img/>').hide().appendTo($('body'));

            var isLoaded = false;
            var isResized = false;

            var originImg = $originImg.get(0);
            originImg.onload = function() { isLoaded = true; };
            originImg.src = originImgUrl;

            var waitInterval = setInterval(function() {
                if (isLoaded) {
                    clearInterval(waitInterval);
                    $img.css('background-image', 'url(' + originImgUrl + ')');
                    $originImg.remove();
                    $img.removeAttr('data-respio-bg');
                    $img.css('height', '');
                }
            }, 0);
        }, 0);
    });

}
```

---
layout: post
title:  "The Truth about DPI & PPI"
date:   2017-05-31 08:00:00 -0400
categories: css
---
For clarity all _units-of-measurement_ will be hyphenated and italicized and all **specific terminology** will be in bold.

## **_PPI_** vs **_DPI_**:

Web pages have no physical dimensions (that's why they can be displayed on an iPhone or from a projector). Because of this, anything measured in _inches_ is meaningless. _Pixels-per-inch_ (**_PPI_**) and _dots-per-inch_ (**_DPI_**) are both hardware and printing related terms.

**_DPI_** has to do with the resolution of a printed image (or word). It's literally how many _dots_ of ink are printed on a piece of paper for every _inch_. The **_DPI_** can be different from the **_PPI_** as I'll explain in a bit. Unfortunately, it has been so commonly misused when **_PPI_** was the appropriate term, that it has become convention.

**_PPI_** has two different meanings, one related to hardware (displays in specific) and one related to printing. When it comes to printing, **_PPI_** is then number of image _pixels_ printed per inch of paper. For example, if you have a 300 x 300 _pixel_ image and print it at 30 **_PPI_** it will be a 10 _inch_ square with 30 _pixels_ across each _inch_. This differs from **_DPI_** because the printer could use more than 30 _dots_ of ink/toner per _inch_. You can see the difference between a printed _dot_ and a printed _pixel_ in the image below. If you look at the top left corner of the R, you can clearly see an approximately 18 _dot_ square. That square is only 1 _pixel_ despite being made up of about 18 _dots_ (which, confusingly, is about 3 _dots-per-pixel_, which isn't a form of measurement I've ever encoutered before).

![Dots per Pixel](/assets/images/the-truth-about-dpi-and-ppi/dots-per-pixel.png)

For hardware, on the other hand, a _pixel_ is exactly what you'd expect it to be: one RGB square of light.

> In fact, pixels are actually made up of “sub-pixels” — red, green and blue light elements that the human eye cannot see because additive color processing blends them into a single hue which appears on the pixel level.

![Sub-pixels](/assets/images/the-truth-about-dpi-and-ppi/sub-pixels.png)

The **_PPI_** of a display is the number of these _pixels_ for every _inch_ of the display and cannot change once the device has been built. For reference, the iPhone 6s display is 1334 x 750 _pixels_ at 326 **_PPI_**, meaning it's screen size is about 4.09 x 2.3 _in_.

## Resizing vs. Resampling:

Whenever you change the **_PPI_** (or **_DPI_** as some image editing programs would have you believe) you are **resizing** an image. This is illustrated below. **Resizing** an image doesn't affect the way it appears on web pages because web pages don't have physical measurements (they're measured only in _pixels_). Sometimes, however, web pages will have to do the **resizing** an image for you in order for it to fit on a page the way it's supposed to. This is a change to the **_PPI_** of the image and is dependent on the display. This is done by the browser and the image properties are unrelated. Note that it is impossible to resize an image to smaller than 1 _image-pixel_ (_CSS-pixel_) to 1 _actual-pixel_ (_device-pixel_). More on different types of _pixels_ later.

![Resizing](/assets/images/the-truth-about-dpi-and-ppi/resizing.png)

**Resampling**, however, is what is important for us when creating images for the web (usually scaling them down so as to reduce web page loading times). The way this works when reducing the **resolution** (the size in _pixels_, e.g. 300 _pixels_ by 300 _pixels_) of an image is the software will take out some of the _pixels_ (every other _pixel_ for example) and then average them out in the _pixels_ that are left over. When increasing the **resolution** of an image, the software will make guesses as to what color the _pixels_ it inserts should be (again, every other _pixel_ for example). This is illustrated below. Note that this is technically what is being done when you **resize** an image to enlarge it or zoom in/out on your display. The inserted _pixels_ are just always the same color as the adjacent _pixels_, resulting in there appearing to be larger _pixels_. When zooming out in your display, **resampling** is done as well, because, as mentioned before, you can't **resize** an image to smaller than 1 _image-pixel_ to 1 _actual-pixel_.

![Resampling](/assets/images/the-truth-about-dpi-and-ppi/resampling.png)

## Types of Pixels and Why They Matter:

When loading a web page, the browser first compares the number of _CSS-pixels_ vs _actual-pixels_ (_device-pixels_). This is called the _pixel-ratio_ (or _device-pixel-ratio_) and for most displays, there's an approximate 1:1 correlation. However, on some displays (retina for example) there can be a 2 or higher _pixel-ratio_. More about _pixel-ratios_ later. When we insert an image into the web page, we tell it how many _CSS-pixels_ it should take up. The browser will then **resample** to display the image smaller than it's original **resolution** or **resize** the image to display it larger (it never **resamples** to enlarge images). In either case, the original image still has to be loaded. That's why the **resolution** should be as close as possible to the _CSS-pixel_ **resolution** (i.e. the size in _CSS-pixels_ that we want the image to be) when uploading an image. If it isn't, than either an unnecessarily large image file will be loaded for the web page, or the image will be **resized**, effectively reducing the **_PPI_** and the quality of the image.

The reason it's important to know whether a display has a _pixel-ratio_ greater than 1 is so that we can take advantage of that to help our images look better. Normally, the way that those displays need to interpret web pages is as follows for a _pixel-ratio_ of 2:

![CSS Device Pixels](/assets/images/the-truth-about-dpi-and-ppi/css-device-pixels.png)

This **resizes** the images and text on a page to keep it from looking too small. Here is an example of what it looks like when this doesn't happen (this may look familiar to some of you):

![Non-resized Images](/assets/images/the-truth-about-dpi-and-ppi/non-resized-images.png)

And here is an example of what it looks like when it does:

![Resized Images](/assets/images/the-truth-about-dpi-and-ppi/resized-images.png)

The difference and usefulness should be clear. However, since we can control which images are displayed based on the _pixel-ratio_ of a display, we can mimic a "perfect" **resampling** to enlarge the image **resolution** instead of just **resizing**. When we tell it to use an image that is twice the **resolution** (which we upload as a separate file) we also tell it to display that image at a 1:1 _pixel-ratio_ so that it takes up the same number of _device-pixels_.

[WebDesignerDepot](http://www.webdesignerdepot.com/2010/02/the-myth-of-dpi/)
[99Designs](https://99designs.com/blog/tips/ppi-vs-dpi-whats-the-difference/)
[FrontEndWebHelp](http://www.frontendwebhelp.com/responsive-design/css-pixels-vs-physical-pixels-device-pixel-ratio/)

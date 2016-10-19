# Old School: Pure Javascript

Create an image slideshow plugin with the following characteristics:

  * Takes a json payload containing an array of *n* images.
  * Adds the slideshow to the targeted element. The element needs to be passed in, and will either be the actual DOM element, or an ID to the desired element. (there may be multiple slidershows on one page)
  * The slideshow should cycles through the images
  * It should display each image for three seconds at a time
  * Clicking the image should skip to the next image
  * When the mouse is over an image, it should pause the slideshow.
  * When the mouse is not longer on the image, it should restart.
  * It should loop back to the first image when the end is reached.

For style points (but will not be counted against you if you do not complete):
- Make it so you can specify which image to start with via the url.
- Loop back to first image is seamless (no visual jump back - seems like the first image was next)
- Manual pagination buttons

**No frameworks, this needs to be pure JS only**

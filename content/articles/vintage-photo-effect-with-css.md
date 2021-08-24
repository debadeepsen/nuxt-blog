---
title: Vintage Photo Effect (pure CSS)
description: Learn how to use @nuxt/content.
---
# Vintage Photo Effect (pure CSS)

If you, like me, were born in the eighties, then you have probably spent some time going through old family photo albums. Back when an album was a physical object that you held in your hands. And even though cameras back in the day lacked the technological prowess of the modern day, the photos were still beautiful in their own way. As a hobbyist photographer myself, I have often used Photoshop and other tools to achieve vintage effects on photos that I took, and simpler tools like Instagram also exist to create similar looks. However, the front-end developer in me got thinking - is there a way to display a photo on the web, like it had been taken in the sixties or seventies? We're in luck, because as it turns out, there is.

## Display only
One thing that I should be clear about is that this is purely a display-based solution. Which means, the image file isn't manipulated in any way, nor can you save the altered image (other than by taking a screenshot).

## What we'll do
To make a photo appear "vintage", we will be doing the following:
* Add a vignette, aka, a gradient-based darkening around the edges, which was often seen in photos taken by old cameras.
* Give the picture a yellowish-orangeish tinge, to give the impression of aging.
* Overlay a subtle film-grainy texture over the image.
* Blur the image a little, because lenses in the past didn't always have tack-sharp focus.
* Make some adjustments to brightness, contrast, and saturation.

We'll be using a few neat things that CSS3 allows us to do, so let's take a look at them now.

## Trick #1: Stacking background images.
CSS3 lets you stack multiple background images, on top of one another. This can be used to create really nice-looking overlays (for example, you could make it look like the sun was peeking from behind the clouds, using different image assets for the sun and the clouds). To do so, we use the `background-image` property, and list the image sources separated by commas (the viewing order is start to finish, i.e., images will be placed over the ones succeeding them in the list).

Another neat thing about CSS is that it treats gradients as images as well. Which means, instead of just chaining multiple images together to form the `background-image` of an element, you could use a mix of gradients and images. We will use this fact to implement various parts of the filter.

#### Vignette
A vignette is literally a radial gradient. We'll use an elliptical shape for it. So the CSS for that would be somewhat like
```css
background-image: radial-gradient(ellipse, #0000, #0007);
```

#### Tint
We can use the `linear-gradient` function to add a translucent overlay. Note that, if we specify a gradient with two colors, and set the same values to both, it will behave as a solid block. While we don't need this technique normally, here we actually do, because remember - gradients are valid background images, regular solid colors are not.
```css
background-image: linear-gradient(0deg, #9725, #9725);
```
> A four digit hex code like `#rgba` is essentially a short way of writing `#rrggbbaa`, where `aa` denotes the alpha channel.

#### Film grain
To obtain this effect, I generated a noise texture on http://www.noisetexturegenerator.com/ and added it as another layer of background image.
```css
background-image: url(https://i.ibb.co/vJt5HSh/noisy-texture-300x300-o10-d10-c-a82851-t1.png);
```

Finally, combining everything and including the actual image to be displayed, we have the following background:
```css
background-image: radial-gradient(ellipse, #0000, #0007),
            linear-gradient(0deg, #9725, #9725),
            url(https://i.ibb.co/vJt5HSh/noisy-texture-300x300-o10-d10-c-a82851-t1.png),
            url(https://i.ibb.co/m4sGRmK/pexels-pixabay-247431.jpg);
```

## Trick #2: CSS filters
CSS filters work in the same way that most photo filters do these days - they add some cool visual effects. The best part is that with CSS, filters can be applied to any element, not just images, which opens up a host of possibilities. We'll use four filters here - `blur`, `saturation`, `brightness`, and `contrast`.
```css
filter: blur(0.05rem) saturate(0.7) contrast(1.5) brightness(1.2);
```
(Values here are only suggested values, play around with them as much as you want until you're satisfied with the result).

## Final CSS and result
With everything above (and a few other things) combined, here's how the final CSS class (I'm calling it `filtered`) would look like - 
```css
.filtered {
    width: 800px;
    height: 450px;
    border-radius: 10px;
    margin: 2px;
    background-image: 
        radial-gradient(ellipse, #0000, #0007),
        linear-gradient(0deg, #9725, #9725),
        url(https://i.ibb.co/vJt5HSh/noisy-texture-300x300-o10-d10-c-a82851-t1.png),
        url(https://i.ibb.co/m4sGRmK/pexels-pixabay-247431.jpg);
    background-size: auto, auto, auto, cover;
    background-repeat: repeat;
    background-position: center;
    filter: blur(0.05em) saturate(0.7) contrast(1.5) brightness(1.2);
}
```
> 1. Just like the `background-image`, the `background-size` and `background-repeat` properties can also be comma-chained. When we write it this way, each segment corresponds to each of the image sources specified as background source.
>
> 2. Unlike with the background, we don't use commas when chaining values for filter. Instead, they are separated by space.

And that should do it. You can check out the sandbox I created below (with a little help from some friendly neighborhood JavaScript) that shows some beautiful photos (thanks to the wonderful https://pexels.com) and their corresponding "aged" versions.

{% codesandbox focused-christian-94ntr %}

One last word, this method is just a suggestion - it's one way to achieve this effect. There are definitely other options. For example, instead of using the `background-image` property of a `div`, you could think of using the `object-fit` property of an `img` element directly. I also used two kinds of gradients here, but the same thing probably could have been done with a `radial-gradient`.

Hope you enjoyed the post. Until next time!

# Augmented Reality on the Web

Tonight we're going to play with augmented reality, using AR.js and A-Frame (the HTML component wrapper around three.js).

## What do we mean when we say "Augmented Reality"?

- **Augmented Reality (AR)** layers information, usually text, atop your view of the physical world.
- **Virtual Reality (VR)** immerses you in a virtual 3D world (so you don't see the physical world).
- **Mixed Reality (MR)** often refers to information and virtual 3D objects layered atop the physical world. 

(I didn't invent these terms, but they may help if you're searching the internet for more information.) What we're doing tonight is technically MR, if we're getting pedantic ...but for our purposes, we'll call it AR.

## How does it work, in basic terms?

1. The device gets sensor data, and/or the camera looks for a pattern. 
   - Usually this is in the form of **markers**, a.k.a. **fiducials**, which have a rotationally asymmetric pattern inside a thick black box. *([ARToolkit](https://artoolkit.org/))*
   - Sometimes, AR code can be trained to look for specific images, without a border, or even 3D objects (like action figures). *([Vuforia](https://www.vuforia.com/))*
   - Sometimes libraries cue on sensor data, or the video of the environment itself, or a combination (e.g., visual inertial odometry). *(Apple's [ARKit](https://developer.apple.com/arkit/), Android's [ARCore](https://developers.google.com/ar/))*
2. The code figures out the **transform** of pattern from the camera image. *(Knowing the proportional size and shape of the pattern, how is it tilted in 3D space?)*
3. The code then uses the perspective of the pattern to **overlay** a 3D object on top of it. Smarter AR libraries will also handle things like **occlusion** *(is there a physical object in front of the virtual object?)* and **lighting** *(what are the ambient light levels in the room? where is the light source?)*.

## See it in action:

https://codepen.io/jeromeetienne/pen/mRqqzb

Use the [Hiro marker](https://jeromeetienne.github.io/AR.js/data/images/HIRO.jpg), which we've printed out for you.

## What is going on here?

- We're using **[AR.js](https://github.com/jeromeetienne/AR.js/#readme)**, which uses ARToolkit, compiled using Emscripten into JavaScript in a way that will run very fast. As well as desktop browsers, you can run it on many Android phones, and on iOS11. 
- It gets a camera feed using **WebRTC**. Due to browser security, your page has to be on HTTPS to request the camera *(although it should work locally, your mileage may vary)*. WebRTC support is the limiting factor (iOS10 doesn't support it).
- AR.js does all of the positioning math and then lets you put a **WebGL** 3D object on top of the camera feed. It's designed to work with **[three.js](https://threejs.org/)**, so the authors adapted it to work with A-Frame.
- **[A-Frame](https://aframe.io/)** is an HTML markup wrapper around three.js, designed to allow people to quickly prototype VR (and now AR) scenes in a browser.

## Walkthrough

Starting from the [codepen example](https://codepen.io/jeromeetienne/pen/mRqqzb), let's change the HTML and play with it. We'll refer to the **[A-Frame reference](https://aframe.io/docs/0.6.0/introduction/)**—I recommend using the search bar, it's often more useful than browsing.

Before we start you may want to change the Editor Layout (under Change View) in Codepen, to give more screen real estate to the camera view.

The code we want to edit is inside the `<body>` element.

```html
<a-scene embedded arjs='trackingMethod: best;'>
  <a-anchor hit-testing-enabled='true'>
    <a-box position='0 0.5 0' material='opacity: 0.5;'></a-box>
  </a-anchor>
  <a-camera-static/>
</a-scene>
```

Everything is wrapped in a three.js scene: `<a-scene>` with an `arjs` attribute. 

`<a-anchor>` is the element for the fiducial marker.

`<a-camera-static/>` is the camera in the scene. (We're using `-static` because we're holding the markers up to our laptop cameras.)

`<a-box>` is the translucent white cube that you see when you hold up the printed Hiro marker. (Hiro is one of two preset markers in AR.js. You can make your own, and there are notes in the next section on how to do that.)

One thing that is helpful to know about A-Frame before you start is that elements can be created in two ways, and either syntax will work. For example, above you have  
`<a-box position='0 0.5 0' material='opacity: 0.5;'></a-box>`  
but you can also write that as  
`<a-entity geometry="primitive: box; position: 0 0.5 0;" material="opacity: 0.5;"></a-entity>`  
and some components have slightly different syntax in their attributes. *The documentation is your friend.*

### Exercises

1. **Change the color** on the cube in the codepen demo. What attribute would you change?  
   *Hint:* look at the Attributes in [the `<a-box>` documentation](https://aframe.io/docs/0.6.0/primitives/a-box.html).
2. **Add text.** Whoa, it's really tiny! Can you make it large enough to read? Can you rotate it and position it?  
   *Hints:* There are two ways to render text, [`<a-text>`](https://aframe.io/docs/0.6.0/primitives/a-text.html) and [`<a-entity>`](https://aframe.io/docs/0.6.0/components/text.html#example).  Also, setting the `width` to `8` should make it …big, but it might also go offscreen.   
   Does the text disappear as you turn the marker?  
   *Hint:* check out [the `side` property](https://aframe.io/docs/0.6.0/components/material.html#properties).
3. **Create a planet Saturn**, by changing the cube to a sphere, and adding a circle or ring. Use `position` to align them.
4. **Add animation.**  
   *Hint:* [documentation here](https://aframe.io/docs/0.6.0/core/animations.html).
5. **Add textures.** Decorate your 3D shapes with a bitmap texture.  
   *Hint:* [documentation here, under `material`.](https://aframe.io/docs/0.6.0/components/material.html#textures)
6. **Import an object.** If you still have time, you can look up 3D models and import an [OBJ](https://aframe.io/docs/0.6.0/components/obj-model.html) or [GLTF](https://aframe.io/docs/0.6.0/components/gltf-model.html) file.



## Next steps, pointers, and pitfalls

If you're moving beyond what we covered tonight, there's a [blogpost on A-Frame's site](https://aframe.io/blog/arjs/) that covers a lot more. Unfortunately, as with many fast-moving web projects, some of the code syntax in the blogpost may be a tiny bit out of date, so you'll want to refer to the code and demos at [AR.js's Github repository](https://github.com/jeromeetienne/AR.js/).

Below are some things to be aware of if you're using it in your own projects...

### You may need to customize `aframe-ar.js`

The current version of `aframe-ar.js` is hardcoded with some values that might not work for you, out of the gate. Don't be afraid to download a copy and mess with the settings. After playing with the high-performing demos on github, I looked at the configuration settings they used and changed `aframe-ar.js` to make it work better. For example...

### Slow it down, shrink it down

I had better luck setting the `maxDetectionRate` at `30`, and the `canvasWidth` to `240`, and the `canvasHeight` to `180`. 

As with OpenCV, AR.js seems to work faster when processing a downsampled video frame.

### Making custom markers

If you want to use custom markers, ...

1. Use https://jeromeetienne.github.io/AR.js/three.js/examples/marker-training/examples/generator.html to generate a `.patt` file, and to (separately) download the image with the black border.
2. When drawing a custom marker, make two versions:
   1. Your full-size marker for printing. White background recommended.
   2. A version resized *(bicubic downsampling)* down to 16x16 pixels, with a light gray (240,240,240) background, for training. The `.patt` file is generated for a 16x16 image, and resizing your image first makes it more easily-recognizable for the camera algorithms.  (Open up a `.patt` file and look inside!)
3. Change `aframe-ar.js` or use the [three.js code](https://jeromeetienne.github.io/AR.js/three.js/examples/mobile-performance.html), instead. The `THREEx.ArToolkitContext.baseURL` in the Github `aframe-ar` library is hardcoded, so you'll need to adjust that, or add your new custom pattern to the list of presets (search the code for `hiro`), and treat it like a preset marker.

### Making custom fonts

If you don't want to use Roboto, Exo, Source Code Pro, or one of the other stock WebVR fonts, you'll need to generate an SDF bitmap font. [The A-Frame docs have a guide.](https://aframe.io/docs/0.6.0/components/text.html#custom-fonts) I used the clunky Java [Hiero](https://github.com/libgdx/libgdx/wiki/Hiero) tool they recommended, and followed [the walkthrough they recommended](https://github.com/libgdx/libgdx/wiki/Distance-field-fonts), and it worked like a charm.

 

## Inspiration & Guidelines

Now that you're *drunk with power*, here are some design guidelines and inspirations for future projects!

### Guidelines

- https://developer.apple.com/ios/human-interface-guidelines/technologies/augmented-reality/
- https://www.uxmatters.com/mt/archives/2009/08/inside-out-interaction-design-for-augmented-reality.php

### Inspirations

- *What would augment reality?* from Luke Wroblewski:
  - https://twitter.com/i/moments/887721492972175360
  - https://storify.com/lukew/what-would-augment-reality
- Google's AR experiments!  
  https://experiments.withgoogle.com/ar
- Made With ARKit (iOS):  
  http://www.madewitharkit.com/
- Zach Lieberman's experiments:
  - https://twitter.com/zachlieberman/status/897945041573879808
  - https://twitter.com/zachlieberman/status/901176633503166468
  - https://twitter.com/zachlieberman/status/901483070171295747
  - https://twitter.com/zachlieberman/status/901785427971072003
  - https://twitter.com/zachlieberman/status/902698224980504576
  - https://twitter.com/zachlieberman/status/902881634642530304
- WorldBrush  
  https://worldbrush.net/
- Keiichi Matsuda's films! (These are *insane* sci-fi visions of AR taken to extremes.)  
  https://vimeo.com/chocobaby

### *Not just visuals* (or: AR can be audio, too)

- Janet Cardiff / George Bures Miller's walks:  
  http://www.cardiffmiller.com/artworks/walks/
- Detour:  
  https://www.detour.com/



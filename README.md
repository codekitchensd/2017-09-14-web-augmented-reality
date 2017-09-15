# Augmented Reality on the Web

Tonight we're going to play with augmented reality, using AR.js and A-Frame (the HTML component wrapper around three.js).

## What do we mean when we say "Augmented Reality"?

- **Augmented Reality (AR)** layers information, usually text, atop your view of the physical world.
- **Virtual Reality (VR)** immerses you in a virtual 3D world (so you don't see the physical world).
- **Mixed Reality (MR)** often refers to information and virtual 3D objects layered atop the physical world. 

(I don't create these terms, these are just what I've seen!) What we're doing tonight is technically MR, but we'll call it AR.

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
- It gets a camera feed using **WebRTC**. Due to browser security, your page has to be on HTTPS to request the camera. WebRTC support is the limiting factor (iOS10 doesn't support it).
- AR.js does all of the positioning math and then lets you put a **WebGL** 3D object on top of the camera feed. It's designed to work with **[three.js](https://threejs.org)**, so the authors adapted it to work with A-Frame.
- **[A-Frame](https://aframe.io/)** is an HTML markup wrapper around three.js, designed to allow people to quickly prototype VR (and now AR) scenes in a browser.



## Walkthrough

_(working on this)_

**[A-Frame reference.](https://aframe.io/docs/0.6.0/introduction/)**

 

## Additional pointers and pitfalls

### You may need to customize `aframe-ar.js`

The current version of `aframe-ar.js` is hardcoded with some values that might not work for you, out of the gate.

### Slow it down, shrink it down

I had better luck setting the `maxDetectionRate` at `30`, and the `canvasWidth` to `240`, and the `canvasHeight` to `180`.

### Making custom markers

If you want to use custom markers, ...

1. Use https://jeromeetienne.github.io/AR.js/three.js/examples/marker-training/examples/generator.html to generate a `.patt` file, and to download the image with the black border.
2. When drawing a custom marker, make two versions:
   1. Your full-size marker for printing. White background recommended.
   2. A version sized down to 16x16 pixels, with a light gray (240,240,240) background, for training. The `.patt` file is generated for a 16x16 image, and resizing your image first makes it more easily-recognizable for the camera algorithms.
3. Hack the `aframe-ar.js` or use the three.js code. The basepath in the github library is hardcoded, so you'll need to adjust that, or add your new pattern to the list of presets, and treat it like a preset marker.

###Making custom fonts

If you don't want to use Roboto, Exo, Source Code Pro, or one of the other stock WebVR fonts, you'll need to generate an SDF bitmap font. [The A-Frame docs have a guide.](https://aframe.io/docs/0.6.0/components/text.html#custom-fonts) I used the clunky Java [Hiero](https://github.com/libgdx/libgdx/wiki/Hiero) tool they recommended, and followed [the walkthrough they recommended](https://github.com/libgdx/libgdx/wiki/Distance-field-fonts), and it worked fine.

 

## Inspiration & Guidelines

### Guidelines

https://developer.apple.com/ios/human-interface-guidelines/technologies/augmented-reality/

https://www.uxmatters.com/mt/archives/2009/08/inside-out-interaction-design-for-augmented-reality.php

### Inspirations

What would augment reality:
https://twitter.com/i/moments/887721492972175360

https://storify.com/lukew/what-would-augment-reality

Google's AR experiments!

https://experiments.withgoogle.com/ar

Zach Lieberman's experiments:

https://twitter.com/zachlieberman/status/897945041573879808

https://twitter.com/zachlieberman/status/901176633503166468

https://twitter.com/zachlieberman/status/901483070171295747

https://twitter.com/zachlieberman/status/901785427971072003

https://twitter.com/zachlieberman/status/902698224980504576

https://twitter.com/zachlieberman/status/902881634642530304

WorldBrush
https://worldbrush.net/

Keiichi Matsuda's films!

https://vimeo.com/chocobaby

### *Not just visuals*

Janet Cardiff / George Bures Miller's walks:
http://www.cardiffmiller.com/artworks/walks/

Detour:

https://www.detour.com/

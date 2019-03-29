![Parallax.js](logo.png)

Parallax Engine that reacts to the orientation of a smart device. Where no gyroscope or motion detection hardware is available, the position of the cursor is used instead.

Check out this **[demo][demo]** to see it in action!

## Setup

Add parallax.js to your project with `npm install --save https://github.com/wagerfield/parallax` or `yarn add https://github.com/wagerfield/parallax`.  
Now, you can require or import the library, depending on your favorite workflow.

```javascript
const Parallax = require('parallax-js')
// or
import Parallax from 'parallax-js'
```

Of course you can also simply copy over the compiled file from the `dist` folder and include it like any other 3rd party script. Make sure to run `npm install` in the Parallax folder to compile the project. Or download the precompiled files from the [release section](https://github.com/wagerfield/parallax/releases).

```html
<script src="dist/parallax.js"></script>
<!-- or if you prefer minified -->
<script src="dist/parallax.min.js"></script>
```

Give each element within your scene a `data-depth` attribute specifying its depth within the scene. A depth of **0** will cause the layer to remain stationary, and a depth of **1** will cause the layer to move by the total effect of the calculated motion. Values inbetween **0** and **1** will cause the layer to move by an amount relative to the supplied ratio.

```html
<div id="scene">
  <div data-depth="0.00"><img src="layer1.png"></div>
  <div data-depth="0.20"><img src="layer2.png"></div>
  <div data-depth="0.40"><img src="layer3.png"></div>
  <div data-depth="0.60"><img src="layer4.png"></div>
  <div data-depth="0.80"><img src="layer5.png"></div>
  <div data-depth="1.00"><img src="layer6.png"></div>
</div>
```

To kickoff a **Parallax** scene, select your parent DOM Element and pass it to the **Parallax** constructor.

```javascript
var scene = document.getElementById('scene')
// or, if you use jQuery
var scene = $('#scene').get(0)

var parallax = new Parallax(scene)
```

## Interactivity

If you need to interact with the layers, don't forget to set the `pointerEvents` option, and adjust your layer CSS.  
Then set an absolute position for all layer child elements, just like it's done in `examples/pages/interactive.html`. Alternatively, set `pointer-events: none` on the layers and `pointer-events: all` on the layer child elements.

## Understanding Layer Motion Calculations

The amount of motion that each layer moves by depends on 3 contributing factors:

1. The `scalarX` and `scalarY` values (see [Behaviours](#behaviours) below for configuration)
2. The dimensions of the parent DOM element
3. The `depth` of a layer within a parallax scene (specified by it's `data-depth` attribute)

The calculation for this motion is as follows:

```javascript
xMotion = parentElement.width  * (scalarX / 100) * layerDepth
yMotion = parentElement.height * (scalarY / 100) * layerDepth
```

So for a layer with a `data-depth` value of `0.5` within a scene that has both the `scalarX` and `scalarY` values set to `10` ( *the default* ) where the containing scene element is `1000px x 1000px`, the total motion of the layer in both `x` and `y` would be:

```javascript
xMotion = 1000 * (10 / 100) * 0.5 = 50 // 50px of positive and negative motion in x
yMotion = 1000 * (10 / 100) * 0.5 = 50 // 50px of positive and negative motion in y
```

## Behaviours

There are a number of behaviours that you can setup for any given **Parallax** instance. These behaviours can either be specified in the markup via data attributes or in JavaScript via the constructor and API.

| Behaviour           | Values              | Default       | Description                                                                                                                                          |
| ------------------- | ------------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `relativeInput`     | `true` or `false`   | `false`       | Specifies whether or not to use the coordinate system of the scene. **Mouse input only.**                                                            |
| `clipRelativeInput` | `true` or `false`   | `false`       | Specifies whether or not to clip the mouse input to the scene bounds. No effect in combination with `hoverOnly`. **Mouse input only.**               |
| `hoverOnly`         | `true` or `false`   | `false`       | Apply the parallax effect only while the cursor is over the scene. Best together with `relativeInput` set to true. **Mouse input only.**             |
| `inputElement`         | `null` or HTML element   | `null`       | Element used for input calculations. Works only with `relativeInput`, might make sense to set `hoverOnly`. When set via `data-input-element` attribute, takes a query selector. **Mouse input only.** |
| `calibrate-x`       | `true` or `false`   | `false`       | Specifies whether or not to cache & calculate the motion relative to the initial `x` axis value on initialisation.                                   |
| `calibrate-y`       | `true` or `false`   | `true`        | Specifies whether or not to cache & calculate the motion relative to the initial `y` axis value on initialisation.                                   |
| `invert-x`          | `true` or `false`   | `true`        | `true` moves layers in opposition to the device motion, `false` slides them away.                                                                    |
| `invert-y`          | `true` or `false`   | `true`        | `true` moves layers in opposition to the device motion, `false` slides them away.                                                                    |
| `limit-x`           | `number` or `false` | `false`       | A numeric value limits the total range of motion in `x`, `false` allows layers to move with complete freedom.                                        |
| `limit-y`           | `number` or `false` | `false`       | A numeric value limits the total range of motion in `y`, `false` allows layers to move with complete freedom.                                        |
| `scalar-x`          | `number`            | `10.0`        | Multiplies the input motion by this value, increasing or decreasing the sensitivity of the layer motion.                                             |
| `scalar-y`          | `number`            | `10.0`        | Multiplies the input motion by this value, increasing or decreasing the sensitivity of the layer motion.                                             |
| `friction-x`        | `number` `0 - 1`    | `0.1`         | The amount of friction the layers experience. This essentially adds some easing to the layer motion.                                                 |
| `friction-y`        | `number` `0 - 1`    | `0.1`         | The amount of friction the layers experience. This essentially adds some easing to the layer motion.                                                 |
| `origin-x`          | `number`            | `0.5`         | The `x` origin of the mouse input. Defaults to 0.5 (the center). `0` moves the origin to the left edge, `1` to the right edge. **Mouse input only.** |
| `origin-y`          | `number`            | `0.5`         | The `y` origin of the mouse input. Defaults to 0.5 (the center). `0` moves the origin to the top edge, `1` to the bottom edge. **Mouse input only.** |
| `precision`         | `integer`           | `1`           | Decimals the element positions should be rounded to. Changing this value should not be necessary anytime soon.                                       |
| `selector`          | `string`            | `null`        | String that will be fed to querySelectorAll on the scene element. You could enter a value of `.layer` to restore old, class-based behaviour |
| `pointerEvents`     | `true` or `false`   | `false`       | Leaving this at false might increase the performance in some instances, while removing pointer events for the scene - eg, Links are not clickable    |
| `onReady`           | `null` or `function`| `null`        | Function that will be called as soon as Parallax setup is completed. Might take up to 1000ms (`calibrationDelay * 2`)    |

In addition to the behaviours described above, there are the methods `enable()` and `disable()` that *activate* and *deactivate* the **Parallax** instance respectively.

### Behaviours: Data Attributes Example

```html
<div id="scene"
  data-relative-input="true"
  data-clip-relative-input="false"
  data-hover-only="true"
  data-input-element="#myinput"
  data-calibrate-x="false"
  data-calibrate-y="true"
  data-invert-x="false"
  data-invert-y="true"
  data-limit-x="false"
  data-limit-y="10"
  data-scalar-x="2"
  data-scalar-y="8"
  data-friction-x="0.2"
  data-friction-y="0.8"
  data-origin-x="0.0"
  data-origin-y="1.0"
  data-precision="1"
  data-pointer-events="false">
  <div data-depth="0.00"><img src="graphics/layer1.png"></div>
  <div data-depth="0.20"><img src="graphics/layer2.png"></div>
  <div data-depth="0.40"><img src="graphics/layer3.png"></div>
  <div data-depth="0.60"><img src="graphics/layer4.png"></div>
  <div data-depth="0.80"><img src="graphics/layer5.png"></div>
  <div data-depth="1.00"><img src="graphics/layer6.png"></div>
</div>
```

### Behaviours: Constructor Object Example

```javascript
var scene = document.getElementById('scene');
var parallax = new Parallax(scene, {
  relativeInput: true,
  clipRelativeInput: false,
  hoverOnly: true,
  inputElement: document.getElementById('myinput'),
  calibrateX: false,
  calibrateY: true,
  invertX: false,
  invertY: true,
  limitX: false,
  limitY: 10,
  scalarX: 2,
  scalarY: 8,
  frictionX: 0.2,
  frictionY: 0.8,
  originX: 0.0,
  originY: 1.0,
  precision: 1,
  pointerEvents: false,
  onReady: function() { alert('ready!'); }
});
```

### Behaviours: API Example

```javascript
var scene = document.getElementById('scene');
var parallax = new Parallax(scene);
parallax.enable();
parallax.disable();
parallax.updateLayers(); // Useful for reparsing the layers in your scene if you change their data-depth value
parallax.calibrate(false, true);
parallax.invert(false, true);
parallax.limit(false, 10);
parallax.scalar(2, 8);
parallax.friction(0.2, 0.8);
parallax.origin(0.0, 1.0);
parallax.setInputElement(document.getElementById('newinput'));
```

## iOS

If you are writing a **native iOS application** and would like to use **parallax.js** within a `UIWebView`, you will need to do a little bit of work to get it running.

`UIWebView` no longer automatically receives the `deviceorientation` event, so your native application must intercept the events from the gyroscope and reroute them to the `UIWebView`:

1. Include the **CoreMotion** framework `#import <CoreMotion/CoreMotion.h>` and create a reference to the **UIWebView** `@property(nonatomic, strong) IBOutlet UIWebView *parallaxWebView;`
2. Add a property to the app delegate (or controller that will own the **UIWebView**) `@property(nonatomic, strong) CMMotionManager *motionManager;`
3. Finally, make the following calls:

```Objective-C
self.motionManager = [[CMMotionManager alloc] init];
if (self.motionManager.isGyroAvailable && !self.motionManager.isGyroActive) {
  [self.motionManager setGyroUpdateInterval:0.5f]; // Set the event update frequency (in seconds)
  [self.motionManager startGyroUpdatesToQueue:NSOperationQueue.mainQueue
                                  withHandler:^(CMGyroData *gyroData, NSError *error) {
    NSString *js = [NSString stringWithFormat:@"parallax.onDeviceOrientation({beta:%f, gamma:%f})", gyroData.rotationRate.x, gyroData.rotationRate.y];
    [self.parallaxWebView stringByEvaluatingJavaScriptFromString:js];
  }];
}
```

## Build

> As a prerequisite, you will need [gulp][gulp] installed: `npm install -g gulp`

```
npm install
gulp
```

gulp will watch the `source` directory for changes and automatically build the `dist` files, serving some demo files with live reload.

## Authors

Matthew Wagerfield: [@wagerfield][twittermw]  
René Roth: [Website][websiterr]

## License

Licensed under [MIT][mit]. Enjoy.

[demo]: http://wagerfield.github.com/parallax/
[twittermw]: http://twitter.com/wagerfield
[websiterr]: http://reneroth.org/
[mit]: http://www.opensource.org/licenses/mit-license.php
[gulp]: http://gulpjs.com/

# Intro to gulls.js

[gulls](https://codeberg.org/charlieroberts/gulls) is a convenience wrapper around WebGPU that makes it easier
to run fullscreen fragment / compute shaders. 

## Setup 

At it's simplest, it looks like this to get a fragment shader on the screen (assuming you have a web server running):

```js
import { default as gulls } from 'https://charlieroberts.codeberg.page/gulls/gulls.js'

// start seagulls, by default it will use the first <canvas> element it
// finds in your HTML page
const sg   = await gulls.init()

// a simple vertex shader to make a quad
const quadVertexShader = gulls.constants.vertex

// our fragment shader, just returns blue
const fragmentShader = `
@fragment
fn fs( @builtin(position) pos : vec4f ) -> @location(0) vec4f {
  return vec4(0., 0., 1., 1. );
}
`

// our vertex + fragment shader together
const shader = quadVertexShader + fragmentShader

// create a render pass
const renderPass = await sg.render({ shader })

// run our render pass
sg.run( renderPass )
```

Place the above text in a file named `main.js`. In the same directory, create a `index.html` file:

```html
<!doctype html>
<html lang=en>

  <head>
    <meta charset=utf-8>
    <script src=main.js type=module></script>
    <style> body { margin:0 } </style> 
  </head>
  
  <body>
    <canvas></canvas>
  </body>
  
</html>
```

If you launch a web server from within the project directory, loading `index.html` should yield a blue window. You're now running a fullscreen fragment shader congrats!

The easiest way to launch a webserver is the following assuming you have node.js installed:
```
npm i http-server
http-server .
```

## If you don't have a web server running...
Skip this section if you have a web server going!

We can still do this without a web server, we just can't run seagulls as a [JavaScript module](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). Instead we have to load it as a "vanilla" JavaScript file, which requires a few modifications.

1. Download [gulls.js](https://codeberg.org/charlieroberts/gulls/src/branch/main/gulls.js) and place the file in your project folder.
2. Add `<script src=gulls.js></script>` to your html file, above the script tag that loads your `main.js` file.
3. Remove the `type=module` attribute from the script tag that loads your main.js file
4. Change the last line of `gulls.js` to be `window.gulls = gulls` instead of the export statement. This effectively makes gulls a "global" variable.
5. Change your `main.js` file to be the following:
```js
// because gulls uses the await keyword, we have to wrap our
// code in an async function and call it. when we use js modules we
// don't have to do this, which is nice... but wrapping isn't that
// big of a deal.
async function run() {
  const sg = await gulls.init()

  // a simple vertex shader to make a quad
  const quadVertexShader = gulls.constants.vertex

  // our fragment shader, just returns blue
  const fragmentShader = `
  @fragment
  fn fs( @builtin(position) pos : vec4f ) -> @location(0) vec4f {
    return vec4(0., 0., 1., 1. );
  }
  `

  // our vertex + fragment shader together
  const shader = quadVertexShader + fragmentShader

  // create a render pass
  const renderPass = await sg.render({ shader })

  // run our render pass
  sg.run( renderPass )
}

run()
```

## Let's pass in our window width / height
If we want to do anything with coordinates in our shader, our CPU needs to tell the GPU the size of our window. We can do this with a shader uniform. Seagulls makes it easy to add a uniform. We'll keep everything from our prior example, and just change the call that creates our render pass:

```js
const renderPass = await sg.render({ 
  shader,
  // add a data array to specify uniforms / buffers / textures etc.
  data:[
    sg.uniform([ window.innerWidth, window.innerHeight ])
  ]
})
```

Let's also change our fragment shader to 1) declare the new uniform and 2) use the new uniform.

```js
const fragmentShader = `
  @group(0) @binding(0) var<uniform> res : vec2f;
  
  @fragment
  fn fs( @builtin(position) pos : vec4f ) -> @location(0) vec4f {
    let uv = pos.xy / res;
    return vec4f( uv.x, 0., uv.y, 1. );
  }
`
```

In our variable declaration we being with a `group` number.  Most of my shaders place almost all of my uniforms in group 0 (with one exception being external video, like webcam feeds). However, for larger shaders you can selectively update individual groups of uniforms for performance purposes. Every uniform has a unique `binding` number for the group it is part of. In seagulls, this number is determined by the position of the uniform in the `data` array you pass to generate the render pass. In this case we only have one uniform, so it is index 0. Next we tell it that it's a uniform (as opposed to a buffer we could read/write to, or a texture), give it a name (`res`) and specify the type.

## A frame counter

Let's add another uniform that captures the current frame count. For this we'll need to:

1. Create another uniform and pass that to our `data` array.
2. Update the value of uniform every frame
3. Declare the uniform in our shader

Our render pass code gets a little more complicated, as we need to define an `onframe` function that will update our uniform. Note that the name `onframe` has nothing to do with the fact that our uniform represents the frame count, `onframe` is just a function that will be called once per frame of video.

```js
let frame = sg.uniform( 0 )
const renderPass = await sg.render({ 
  shader,
  // add a data array to specify uniforms / buffers / textures etc.
  data:[
    sg.uniform([ window.innerWidth, window.innerHeight ]),
    // make sure this is second so it gets bound to index 1
    frame
  ],
  onframe() { frame.value++ }
})
```

```js
const fragmentShader = `
  @group(0) @binding(0) var<uniform> res   : vec2f;
  @group(0) @binding(1) var<uniform> frame : f32;
  
  @fragment
  fn fs( @builtin(position) pos : vec4f ) -> @location(0) vec4f {
    let uv = pos.xy / res;
    return vec4f( uv.x, .5+sin(frame/60.)*.5, uv.y, 1. );
  }
`
```

After these changes the green in the image should be rising and falling in a sinusoidal pattern.

## A slider 
The steps for adding a slider to control our shader are similar to the ones for our frame counter, but we need to do a few extra things:

1. Add the slider to our `index.html` file (via the `<input type=range>` element)
2. Get a reference to our slider using JavaScript
3. Define an additional callback for whenever the slider value is changed.

First, add the following element to your HTML:
```html
    <input 
       type=range 
       id=slider 
       min=0 max=4 
       step=any 
       style='position:absolute; left:0; top:0; z-index:1'
    /> 
```

The `step` attribute lets the slider use arbitrary increments. The `style` attribute adds some CSS that will position our slider in the upper left corner of our window, and above our running shader.

Now in our `main.js` file:

```js
const slider = document.querySelector('#slider')

// create a render pass
let frame_u  = sg.uniform( 0 )
let slider_u = sg.uniform( slider.value )
const renderPass = await sg.render({ 
  shader,
  data:[
    sg.uniform( [window.innerWidth, window.innerHeight] ),
    frame_u,
    slider_u
  ],
  onframe() {
    frame_u.value++
  }
})

// our sliders value returns a string, so we'll convert it to a
// floating point number with parseFloat()
slider.oninput = ()=> slider_u.value = parseFloat( slider.value )
```

Note that I changed the name of the `frame` variable to `frame_u`, the `_u` is just a naming convention to show me that the variable is storing a uniform.

... and on to our shader:

```js
const fragmentShader = `
  @group(0) @binding(0) var<uniform> res   : vec2f;
  @group(0) @binding(1) var<uniform> frame : f32;
  @group(0) @binding(2) var<uniform> slider: f32;

  @fragment
  fn fs( @builtin(position) pos : vec4f ) -> @location(0) vec4f {
    let uv = pos.xy / res * slider;
    return vec4f(uv.x, .5+sin(frame/60.)*.5, uv.y, 1. );
  }
`
```

You can add multiple sliders / buttons / other interactive widgets this way. There's also a [howto](https://charlieroberts.codeberg.page/gulls/howtos/5_tweakpane) that demonstrates how to use the popular [Tweakpane library](https://tweakpane.github.io/docs/) with gulls.js.

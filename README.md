pixi.js-keyboard (2021 Pixi.js v5)
======================

Handle keyboard events elegantly and simple.

Demo: http://134.122.95.172/pixijs/

Download: http://134.122.95.172/pixijs/demo.zip (browserify)

## Installation
```
npm install --save pixi.js-keyboard pixi.js-mouse
```

### Reference
Keycodes: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code/code_values

## API
### Keyboard
#### .isKeyDown('KeyW')
Checks if the key is held.
#### .isKeyPressed('KeyW')
Checks if the key was pressed, holding will not trigger this more than once.
#### .isKeyReleased('KeyW')
Checks whether the key was just released.
#### .update()
Ensure to use this for correct event handling at the end of the game loop.
#### .events.on('pressed', null, (keyCode, event) => { console.log(keyCode); });
Callback when any key is pressed.
#### .events.on('released', null, (keyCode, event) => { console.log(keyCode); });
Callback when any key is released.
#### .events.on('down', null, (keyCode, event) => { console.log(keyCode); });
Callback when any key is down.
#### .events.on('pressed_KeyW', null, (keyCode, event) => { console.log(keyCode); });
Callback when a particular key is pressed.
#### .events.on('released_KeyW', null, (keyCode, event) => { console.log(keyCode); });
Callback when a particular key is released.
#### .events.on('down_KeyW', null, (keyCode, event) => { console.log(keyCode); });
Callback when a particular key is down.

## Example
### index.js
```js
const PIXI = require('pixi.js');
const Keyboard = require('pixi.js-keyboard');
const Mouse = require('pixi.js-mouse');
 
//Aliases
var loader = PIXI.Loader.shared;
   
//Create a Pixi Application
var app = new PIXI.Application({
  width: document.documentElement.clientWidth, 
  height: document.documentElement.clientHeight,                       
  antialiasing: true, 
  backgroundAlpha: 0.1, 
  resolution: 1
});
 
window.addEventListener("resize", function() {
  app.renderer.resize(document.documentElement.clientWidth, document.documentElement.clientHeight);
});
 
app.view.addEventListener('contextmenu', (e) => {
  window.wasRightClick = true;
  //e.preventDefault();
});
 
//Add the canvas that Pixi automatically created for you to the HTML document
app.view.id = 'main';
document.body.appendChild(app.view);
 
//Use Pixi's built-in `loader` module to load an image
var sprites = {};

loader.add("bunny", "images/bunny.png");

// The `load` method loads the queue of resources, and calls the passed in callback called once all resources have loaded.
loader.load((loader, resources) => {
  // resources is an object where the key is the name of the resource loaded and the value is the resource object.
  // They have a couple default properties:
  // - `url`: The URL that the resource was loaded from
  // - `error`: The error that happened when trying to load (if any)
  // - `data`: The raw data that was loaded
  // also may contain other properties based on the middleware that runs.
  sprites.bunny = new PIXI.Sprite(resources.bunny.texture);
});

// throughout the process multiple signals can be dispatched.
loader.onProgress.add(() => {}); // called once per loaded/errored file
loader.onError.add(() => {}); // called once per errored file
loader.onLoad.add(() => {}); // called once per loaded file
loader.onComplete.add(setup); // called once when the queued resources all load.

var state;
 
function getAngleTo(mx, my, px, py) {
  var self = this;
  var distX = my - py;
  var distY = mx - px;
  var angle = Math.atan2(distX, distY);
  // var degrees = angle * 180/ Math.PI;
  return angle;
}
 
function getAngleX(length, angle) {
  return Math.cos(angle) * length;
}
 
function getAngleY(length, angle) {
  return Math.sin(angle) * length;
}
 
function setup() {
  const bunny = sprites.bunny;
  
  // Introduce the `bunny` sprite 
  bunny.position.set(100, 100);
  bunny.anchor.set(0.5, 0.5);
  app.stage.addChild(bunny);
  
  // Set the game state
  state = play;
 
  // Start the game loop 
  app.ticker.add(delta => gameLoop(delta));
  
  Mouse.events.on('released', null, (buttonCode, event, mouseX, mouseY, mouseOriginX, mouseOriginY, mouseMoveX, mouseMoveY) => {
    console.log(buttonCode, mouseOriginX, mouseOriginY, mouseX, mouseY, mouseMoveX, mouseMoveY);
  });
}
 
function gameLoop(delta){
  // Update the current game state:
  state(delta);
 
  Keyboard.update();
  Mouse.update();
}
 
function play(delta) {
  const bunny = sprites.bunny;
  const speed = 5 * delta;
  
  // Keyboard
  if (Keyboard.isKeyDown('ArrowLeft', 'KeyA'))
    bunny.x -= speed;
  if (Keyboard.isKeyDown('ArrowRight', 'KeyD'))
    bunny.x += speed;
  
  if (Keyboard.isKeyDown('ArrowUp', 'KeyW'))
    bunny.y -= speed;
  if (Keyboard.isKeyDown('ArrowDown', 'KeyS'))
    bunny.y += speed;
  
  // Mouse
  bunny.rotation = getAngleTo(Mouse.getPosX(), Mouse.getPosY(), bunny.x, bunny.y);
  
  if (Mouse.isButtonDown(Mouse.Button.LEFT)) {
    bunny.x += getAngleX(speed, bunny.rotation);
    bunny.y += getAngleY(speed, bunny.rotation);
  }
  
  if (Mouse.isButtonDown(Mouse.Button.RIGHT)) {
    bunny.x -= getAngleX(speed, bunny.rotation);
    bunny.y -= getAngleY(speed, bunny.rotation);
  }
}
```

### index.html

```html
<!doctype html>

<html lang="en">
  <head>
    <meta charset="utf-8">

    <title>Example</title>
    <style>
      body, main {
        margin: 0;
        padding: 0;
      }
      
      body {
        overflow: hidden;
      }
    </style>
  </head>

  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

### Browserify

Install globally: `npm install -g browserify`

Build bundle.js: `browserify index.js -o bundle.js`

### Or download the example

Download: http://134.122.95.172/pixijs/demo.zip
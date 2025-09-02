<img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" />

# :snake: Snake game

Snake game coded in **vanilla JavaScript** with keyboard controls and retro gaming design.

We used 2 JavaScript APIs: 
- **Canvas API** → drawings & animations.
- **Web Audio API** → the sound design.


## :video_game: Play the game

Choose a difficulty and click on "Start".

Use the arrow key directions on your keyboard to move the snake. The goal is to eat as many flowers as you can.

You can reset the game at any time by clicking on "Replay".

### Rules Reminder
- The goal is to eat food (e.g., apples, flowers, dots) that appears randomly on the grid.
- Each time the snake eats, it grows longer and the score increases.
- The game ends if the snake:
    - hits the walls (border of the play area), or
    - runs into its own body.

- The challenge is to survive as long as possible while making the snake as long as you can.


## :bulb: Conception

### API Canvas

#### Main concepts (Patrick)

grid / drawing
animation : RefreshAnimationFrame

### Snake (Patrick)
position of all the elements composing the snake in an array, drawing elements & animation

### Food = Flower (Paul)

To represent the food, we initially chose a simple square as the one used to create the snake body.
But it seems more fun to have a rotating flower to symbolize the food 😅​.

#### Random positioning
To set a random (X,Y) positionning of the flower in the grid (from (0,0) to (xCells, yCells) - grid size set in config.js), we use this function on x and y coordinates : 
```js
function randomCellPosition(cellsNumber) {
    return Math.floor(Math.random() * cellsNumber);
}
```
It returns en integer between 0 and cellsNumber.
So we got the (X,Y) coordinates of the flower in the grid, that will be used later to check if the snake's head is on the same cell as the flower.

#### Convert grid → pixel position to draw the flower
The drawing in Canvas API is based on pixels. So we use a function to get the (x,y) coordinates in pixels of the center of the flower : 
```js
function cellToPx(cellIndex) {
    return cellIndex * GRID_SIZE + 0,5 * GRID_SIZE ;
}
```
Those values are saved in constants (px, py).

#### Animation = Rotation with friction
To draw the flower, we have to translate the "ctx" to the flower's center.

```js
ctx.save();           // save the canvas state so that the rest of the drawing isn’t affected.

ctx.translate(px, py); // (0,0) becomes the flower’s center
ctx.rotate(flowerAngle); // rotate around the center

ctx.restore(); // Cancels the rotation/translation for the rest of the drawing.

```

#### Flower geometry
The flower should be embedded in a grid's cell (diameter < 20 px). So we chose those dimensions
```js
const petalR = 4;   // petal radius in px
const centerR = 3;  // center radius in px
const offset = 5;   // distance from center to each petal in px
```
- The petal centers are placed on a small circle of radius offset around the flower’s center.
- Each petal is a small disk with radius petalR.
- Quick check to fit inside a 20 px cell: 2 x (offset + petalR) = 2 x (5 + 4) = 18 ≤ 20 px → it fits.


#### Draw the center
We choose 5 petals instead of 4 to make the rotation easier to see.
```js
ctx.fillStyle = "#ff0000"; // center's color
ctx.beginPath();
ctx.arc(0, 0, centerR, 0, Math.PI * 2);
ctx.fill();
```
The heart of the flower is a red disk at the local center (0,0), with a radius = centerR.  
```Math.PI * 2``` = one full circle in radians (360°).


#### Draw the petals (relative coordinates to flower's center)
We choose 5 petals rather than 4 instead of 4 to make the rotation easier to see.
```js
for (let i = 0; i < 5; i++) {
    ctx.fillStyle = "#ffd34d";
    const a = i * (2 * Math.PI / 5);        // 360° / 5 = 72°
    
    // petal's center coordinates from the flower's center
    const pxr = Math.cos(a) * offset;
    const pyr = Math.sin(a) * offset;
    
    ctx.beginPath();
    ctx.arc(pxr, pyr, petalR, 0, Math.PI * 2);
    ctx.fill();
}
```
- Petals are evenly spaced on a circle: 360° / 5 = 72°.
- Polar → Cartesian: `x = cos(a) * radius (= offset)`.

#### Rotation
The rotation, applied every time `updateGame()` is run, is based on the `flowerAngle` in radians.  
We apply a friction factor to the rotation speed so that the spinning gradually slows down until it eventually stops.  

Before each rotation update, we call this function on `flowerAngle`:
```js
function slowDownFlowerRotation(flowerAngle) {
    speed *= friction;
    return (flowerAngle + TAU * speed / 60) % TAU;
}
```

- speed → a global (or outer-scope) variable representing the current rotation speed of the flower in turns per second (initially set to 6).

- friction → a multiplier slightly less than 1 (0.95 here) that reduces speed over time, simulating a slowdown.

- TAU → shorthand for 2 * Math.PI (≈ 6.283), representing one full circle in radians.


## Game (Paul)
GameStart
UpdateGame : snake moving, eating food
GameEnding : collision on the borders, or snake eating itself


## :art: Design UI/UX (Patrick)

[Subframe - Pixel Legends Showcase]
(https://www.subframe.com/tips/css-pixel-art-examples)

Inspiration by the design to create our interface with retro pixel design and audio sound. 

## :sound: Sound design (Patrick)

Using Web Audio API and examples from Subframe

## :computer: Refactoring (Patrick : modules, Paul : class)

Modules
Class Game


## 👨‍💻 Authors
Coded by @Pololac & @PatrickLaubscher.
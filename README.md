# Creative Major Project - Individual Prototype File

## 1 Introduction
**Hello! My name is Alice and welcome to my individual functioning prototype!** 

I am in Tut1 Group C and I’ve created a group code with Wensi, Viki and Yuchen. We decided to abstract *Wheels of Fortune* by Pacita Abad and here is my take on expanding on the group code.  

Upon seeing my artwork, it would be an empty canvas with a gradient background and meteors dashing diagonally. 
It requires several mouse clicks and key presses interaction to activate the artwork: 

1. To see circles on the canvas, press anywhere using your mouse
2. A prompt will pop up asking you to press a number between 1-4 on your keyboard. The number you choose will determine what circle is outputted.
3. You can resize these circles by clicking in the middle of the circle and dragging your mouse inwards or outwards
4. To clear the canvas, press the space bar

## 2 Design Approach
### 2.1 Method Chosen: User Input
I chose to differ my artwork by using user input and my approach when designing the effects is that I want to make it personalised for each user so they can be in control of what is being displayed on the canvas. This is achieved through mouse clicks, mouse drag and keypresses. For each interaction, it will display a different output. 

### 2.2 Pulsing Animation
After selecting the circle style via the prompt, the circle appearance will exhibit a three-second pulsation and the contents inside each circle will remain rotating. This is created to visually communicate that a new circle has been spawned onto the canvas giving users feedback whilst making it interesting. 


### 2.3 Fade-Out Animation
Instead of clearing the canvas after space bar press, I wanted to create the effect of the circles fading away to make it more interesting and to match how the circles were spawned onto the canvas. 

### 2.4 Prototype Difference with Teammate 

The major difference between my work and other team member’s work is that I started with an empty canvas that required user input to create the artwork. My artwork also primarily focuses on personalisation rather than automatic animation like the others. 

My teammates have chosen the following methods to animate their artwork:

**Viki**: Audio – Circle vibrates to the music played  
**Wensi**: Perlin Noise – Incorporates a rainbow filter animated over the artwork whilst the circle flows around the canvas.  
**Yuchen**: Time-Based – The speed of circle rotation and meteors dashing across the canvas changes over time.

## 3 Inspirations
I took inspiration from the pulsing animation in this [artwork](https://www.pond5.com/stock-footage/item/70333529-expanding-blue-concentric-rings-loop-abstract-animation) where the rings of the circle continue to expand. 
Inspired by this, I wanted to also make the ring of the circles expand when they appear on the canvas. But I took a slightly different approach, instead of allowing them to expand continually, I wanted the users to be able to adjust the size to their liking.  

## Technical Code Explanation
Here is a breakdown of the individual code that I have incorporated into the group code to deliver the inteded effects.

#### 1. Appends circle based on mouse drag and circle style selection from the user
First, variables are defined so they can be used in later functions
```
// New variables to facilitate animation and effects based on user input
let selectedCircle = null; // Checks to see if circle is selected by user
let userCircleStyle = null; // Gets input value from the user (1-4)

```
Then **mousePressed()** function is created where it contains prompts and appends the circle based on the user’s input circle style by tracking user's mouse positions

```
function mousePressed() {
  for (let i = 0; i < cirs.length; i++) {
    let d = dist(mouseX, mouseY, cirs[i].x, cirs[i].y);
    if (d < cirs[i].cirSize) {
      selectedCircle = cirs[i];
      isDragging = true;
      break;
    }
  }
  // A prompt would appear after mouse press is detected and it will appends a new circle based on the input style
  if (!selectedCircle) {
    userCircleStyle = prompt('Create your memory circle by selecting a style between 1-4):');
    if (userCircleStyle >= 1 && userCircleStyle <= 4) {
      let newCircle = new Circle(mouseX, mouseY, userCircleStyle, 100, color(random(255), random(255), random(255)));
      cirs.push(newCircle);
    } else {
      alert('Invalid circle style! Please select a number between 1 and 4.');
    }
  }
}
```
#### 2.	Pulsating animation when the circle is spawned on canvas

More variables are defined first.

```
let pulseStartTime = null; // Circle apperance animation time
let isPulsing = false; // Checks if animation is active
let pulsingCircle = null; // Checks which circle has animation
```

The pulsing animation effect is added to the **draw()** function and the effect is done by keeping track of the time using **millis()** and changing the size of the circle using **map()**

```
  // Pulse animation for circle appearance based on user input (in prompt and click)
  if (isPulsing && pulsingCircle) {
    let elapsed = millis() - pulseStartTime; //millis() keeps track of how long a sketch has been runing in miliseconds
    let pulseDuration = 3000; // 3 seconds of pulsing
    let pulseSize = map(elapsed, 0, pulseDuration, pulsingCircle.originalSize, pulsingCircle.originalSize * 1.5);
    pulsingCircle.cirSize = pulseSize;
    // Stops the pulsing animation after duration
    if (elapsed > pulseDuration) {
      isPulsing = false;
      pulsingCircle = null;
    }
  }
```

Updating this in the **mousPressed()** function so the animation will occur when the circle is selected

```
    if (userCircleStyle >= 1 && userCircleStyle <= 4) {
      let newCircle = new Circle(mouseX, mouseY, userCircleStyle, 100, color(random(255), random(255), random(255)));
      cirs.push(newCircle);
      // Trigger pulse animation for the newly added circle
      pulsingCircle = newCircle;
      pulsingCircle.originalSize = newCircle.cirSize;
      pulseStartTime = millis();
      isPulsing = true;
    } else {
      alert('Invalid circle style! Please select a number between 1 and 4.');
    }
```

#### 3.	Dragging to resize the circle 

Defining another variable

```
let isDragging = false;

```
Creating a new function called the **mouseDragged()** and **mouseRelease()** which checks if mouse is on the canvas using booleans and keeping track of the mouse’s x and y position

```
// Using the mousePressed() function, it resizes the circle if it has been dragged 
function mouseDragged() {
  if (isDragging && selectedCircle) {
    let newSize = dist(mouseX, mouseY, selectedCircle.x, selectedCircle.y) * 2;
    selectedCircle.cirSize = newSize;
  }
}
// This checks if user is still pressing the mouse
function mouseReleased() {
  isDragging = false;
  selectedCircle = null;
}
```
#### 4.	Creating a fadeout animation to remove existing circles on canvas via space bar press

New Variable is declared

```
Arrary to append circles that needs to be removed
```

A **FadingCircle()** class is created to make the fad-out effect for the exisiting cirles on canvas. It will be triggered when space bar is pressed

```
// Class representing circle on canvas fading away when space bar is pressed
class FadingCircle {
  constructor(circle) {
    this.circle = circle;
    this.fadeStep = 2;
    this.alpha = 255; // Initial opacity
  }

  // Determines how fast the circle would fade out
  fadeOut() {
    this.circle.cirSize -= this.fadeStep;
    this.alpha -= this.fadeStep * 2;
    this.circle.display(this.alpha);
  }

  // Checks if circle has faded away by checking it's size and opacity (alpha)
  isFaded() {
    return this.circle.cirSize <= 0 || this.alpha <= 0;
  }
  
  display() {
    this.circle.display(this.alpha); // Pass alpha to the display method of Circle
    // Update particles' display with the alpha value
    for (let i = 0; i < this.circle.parts.length; i++) {
      this.circle.parts[i].display(this.alpha);
    }
    // Particle 2
    if (this.circle.parts2) {
      for (let i = 0; i < this.circle.parts2.length; i++) {
        this.circle.parts2[i].display(this.alpha); // Display Particle 2 with fading alpha
      }
    }   
  }
}
```
Creates a **keypress()** function where when the space bar is pressed it calls the **FadingCircle()** class to trigger the effect

```
// Space bar press to clear all circles so the user can start again
function keyPressed() {
  // if (keyCode === 32) { // Space bar pressed
  //   cirs = []; // Clear all circles
  // }
  if (keyCode === 32) { // Space bar pressed
    for (let i = cirs.length - 1; i >= 0; i--) {
      fadingCircles.push(new FadingCircle(cirs[i]));
      cirs.pop();
    }
  }
}
```
In this section I used splice() which is referenced from [here](https://p5js.org/reference/p5/splice/)

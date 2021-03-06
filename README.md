# A very stupid game implemented in Racket
#### New Rules of the Game
1. make objects that would be touching the paddle, if they were
   at the bottom of the screen, look different. That is, help the
   player to understand the extent of the paddle with a subtly
   different faller image. (This applies to the new types of fallers
   you add for the second part of the homework too.) 
   - See *normal-image* and *touch-image* in the new state variable **list-of-faller**
2. make a new type of faller, such that, when it touches the paddle,
   the paddle gets wider and another such that, when it touches the
   paddle, the paddle gets narrower. These fallers should appear at
   regular intervals (not randomly) in your game. For example, every
   10th faller could be a shrinking faller, say.
   - See new state variable **list-of-faller**, and new state variable **paddle**--- especially the new definititions *paddle-stretch* and *paddle-image*.


#### 4 state variables of the world now: 
- `(define-struct world (paddle fallers score tick))`   
- `(make-world paddle list-of-faller number number)`
- a **paddle** is
  * `(make-paddle stretch direction image number)` 
  * `(define-struct paddle (stretch direction image x))`
    + a *stretch* indicates if paddle is expanding/shrinking/staying constant
      - "expand"
      - "shrink"
      - "constant"
    + a *direction* is either indicates which direction paddle is moving in
      - "left"
      - "right"
    + the *image* is just the paddle image and the default image is 
      - `(define PADDLE-IMAGE (rectangle 50 12 "solid" "black"))`
      - each `tick' we either decrease the width of image or increase the width of the image depending on *stretch*
      - There is a min and max amount that paddle can shrink/expand
        * `(define MAX-PADDLE-WIDTH (/ WORLD-WIDTH 4))`
        * `(define MIN-PADDLE-WIDTH 5)`
    + *x* 
      - represents the x-corrdinate of the paddle
- A **list-of-faller** is either 
  * `'()`
  * `(cons faller list-of-faller)`
    - A *faller* is
      * (make-faller faller-image number number)
      * (define-struct faller (image x y))
      * A *faller-image* is either 
        - A *normal-image*
          * `FALLER-IMAGE`
          * `EXPAND-FALLER-IMAGE`
          * `SHRINK-FALLER-IMAGE`
        - A *touch-image*
          * `TOUCH-MAIN-IMAGE`
          * `EXPAND-TOUCH-FALLER-IMAGE`
          * `SHRINK-TOUCH-FALLER-IMAGE`
       * x & y are the x-corr y-corr of the paddle
- A **score** simply records the score of the game
- A **tick** just counts how many ticks have passed in the game. This is primarily so we can add "shrinking" and "expanding"
 fallers every `SHRINK-TICK` and `EXPAND-TICK` intervals. I'm using the remainder function to do this

#### How I have been updating
For each function `tick-tock` `click-click` and `render` I create functions to update each state variable. To make this more precise
the ones I have finished have :alien: next to them, and the one I am currently working on have :broken_heart: next to them. 

**`tick-tock`**:broken_heart:  every tick of the clock everything needs to be updated 
- `(make-paddle stretch direction image number)` :broken_heart:
  * stretch (This depends on `list-of-faller` so needs filter/map/reduce)  :alien:
  *  direction :alien:
  *  image :alien:
  * `number' which is the x-corrdinate of paddle :alien:
- `list-of-faller` :broken_heart:
  * `(make-faller faller-image number number)`
    - faller-image :alien:
    - x & y coordinate update concurrently (Maybe we should switch this back to posn?) :alien:
  * Update using with filter/map/reduce on functions created above ^^^ :broken_heart:
- `score` :alien:
  * Depends on `list-of-faller` so needs filter/map/reduce :alien:
- `tick`  - super simple (add1 tick) :broken_heart:

**`click-click`** Every time we hit spacebar I think we only need to update the `score` and `paddle-direction`? 
- `(make-paddle stretch direction image number)`
  * ~~stretch (This depends on `list-of-faller` so needs filter/map/reduce)~~
  * direction
  * ~~image~~
  * ~~`number' which is the x-corrdinate of paddle~~
- ~~`list-of-faller`~~
  * ~~`(make-faller faller-image number number)`~~
    - ~~faller-image~~
    - ~~x & y coordinate update concurrently (Maybe we should switch this back to posn?)~~
  * ~~Update using with filter/map/reduce on functions created above ^^^~~
- `score`
  * Updating score depends on `list-of-faller` so needs filter/map/reduce
- ~~`tick`~~  

**`render`** Everytime we call render we need to show `paddle` `score` `list-of-faller` on the canvas
- `(make-paddle stretch direction image number)`
  * ~~stretch (This depends on `list-of-faller` so needs filter/map/reduce)~~
  * ~~direction~~
  * image
  * `number' which is the x-corrdinate of paddle
- `list-of-faller`
  * `(make-faller faller-image number number)`
    - faller-image
    - x & y coordinate update concurrently (Maybe we should switch this back to posn?)
  * Update using with filter/map/reduce on functions created above 
- `score`


The current commit in this repo commments out the parts that are not ready. So a majority of the code is commented out. For the parts that are not commented out you can
run test and they work just fine. I've just been creating functions (or adjusting existing functions) for each individual component and slowly uncommenting blocks of code iteratively.  So I first started out by creating
functions to update `stretch, direction, image, number` for the paddle every tick. Then I moved on to creating functions to update `score` every tick, then moved on to creating functions to update the components
that make up `list-of-faller` every tick. The next thing on my plate is to create a function that updates the entire paddle every tick by calling the functions I created for
`stretch, direction, image, number`, and then similarily I will create a function that ties in the functions I created for `list-of-faller` to update the *entire* `list-of-faller` every tick. After that I
will tie all the functions I created together and create **`tick-tock`** to update the *entire world* every tick.

When creating the individual components functions I make sure the input is only what I need. For example, the input for the function that updates the x-corrdinate of the paddle every tick is just the paddle-corr and the paddle-direction, `update-paddle\x: number string -> number`, and not the entire paddle object itself. Similarily,
to update the paddle-direction the input for this function is paddle-corr, paddle-direction, and paddle-image: `update-paddle\direction: number string -> string`. I found that
doing things this way and testing each iteration makes things easy --barely any thinking--  very boring.

  

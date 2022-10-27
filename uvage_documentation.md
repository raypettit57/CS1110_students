# UVa Game Engine (uvage)

### Necessary structure

Every uvage program has three parts. It begins with this exact code:

````python
# beginning: must come first
import uvage
camera = uvage.Camera(800, 600)
````

You can change the 800 and 600 to be the width and height of the window you want to make instead.

The middle will usually create various gameboxes to be used later. For example:

````python
# prep work: make the gameboxes to be used later
logo = uvage.from_image(0, 0, "https://www.python.org/static/img/python-logo.png")
````

Every uvage program should end with an event loop, this is often named `tick`:

````python
# make a parameter-less method that will be called every frame.
def tick():
    if uvage.is_pressing("up arrow"): # you can check which keys are being pressed
        print(" the up arrow key is currently being pressed")
    if camera.mouseclick: #true if some mouse button is being pressed
        logo.center = camera.mouse # the current mouse position
    camera.draw(logo)
    camera.display() # you almost always want to end this method with this line

# tell uvage to call the tick method 30 times per second uvage.timer_loop(30, tick)
# this line of code will not be reached until after the window is closed
````

# gameboxes

A gamebox is an abstraction of an image or block of color inside a rectangle.
They can move, bump into one another, and change appearance.

There are four common ways to make a gamebox: from a color, an image, some text, or another gamebox.
All begin with the `x, y` of the center of the gamebox.

````python
b1 = uvage.from_color(50, 100, "red", 20, 40) # x, y, color, width, height
b2 = uvage.from_image(10, 30, "https://www.python.org/static/img/python-logo.png")
                       # x,  y,  url_or_filename
b3 = uvage.from_text(50, 100, "Hi", 12, "red", italic=True)
    # x, y, text, size, color; optionally True/False for bold ​and italic too
b4 = b2.copy_at(50, 100) # x, y
````

You can move, rotate, mirror-image, and resize gameboxes and change their image or color:

````python
b1.move(5, 6)         # move 5 pixels rightward and 6 pixels downward
b1.x -= 5             # move 5 pixels leftward
b1.left = 20          # puts edge at particular place (right/top/bottom work too)
b1.x = 20             # puts midline at particular place (y works too)
b1.center = [50, 100] # centers box at particular location

b1.speedx = 10  # can set speed in x and y
b1.move_speed() # and move at current speed

b2.rotate(20)       # rotates 20 degrees (doesn't work for color boxes)
b2.flip()           # mirror image
b2.scale_by(0.5)    # half current size
b2.full_size()      # as large as original image was
b2.width = 20       # scales uniformly so that width becomes 20
b2.size = [20, 200] # stretch and squash to make box exactly 20 by 200

b1.color = "green"                             # becomes a green color box
b1.image = "python.org/images/python-logo.gif" # becomes an image box
````

gameboxes also can detect and remove collisions:

````python
print(b1.touches(b2))         # True if they touch, False if they don't
print(b1.touches(b2, -5, 10)) # if overlap by at least 5 in x and within 10 in y
print(b1.bottom_touches(b2))  # True if b1's bottom edge touches b2
print(b1.contains(17,21))     # True if the point (17, 21) is inside b1

b1.move_to_stop_overlapping(b2)      # b1 will move, not b2 (also updates b1's speed)
b1.move_both_to_stop_overlapping(b2) # b1 and b2 move same amount (and update speed)
````

The "padding" parameters (−5 and 10 above) may be added to any "touches" or "overlapping" method.

# Cameras

You can only make one camera per program. The camera controls what is on the screen.
What the camera `draw`s is invisible until after you ask it to `display` what it drew.

````python
camera = uvage.Camera(width, height)
print(camera.width)  # prints the width of the camera's viewport
camera.clear("red")  # fills the screen with red

camera.draw(someBox)                  # draws a gamebox
camera.draw("Hi", 12, "blue", 15, 30) # draws text in 12-point font at (15, 30)

camera.display()       # makes what's currently drawn visible
camera.move(3, 5)      # moves the camera 3 pixels left and 5 pixels down
camera.left = 100      # makes the left edge of the screen be at x=100
                       # .right, .top, .bottom, .x, and .y also work
camera.center = [5, 5] # centers the camera on point (5, 5)
                       # .topleft, .topright, .bottomleft, and .bottomright also work
````


# Animation

Animation is created by changing the image in a gamebox from frame to frame.
Lots of example animations can be found via an image search for "`sprite sheet filetype:png`".
Sprite sheets have a grid of frames and can be turned into a list like so:

````python
# load a grid of 3 rows and 4 columns as a list of 12 images
sheet = uvage.load_sprite_sheet("https://upload.wikimedia.org/wikipedia/commons/b/be/SpriteSheet.png", 3, 4)

# make a gamebox image from one of those images
b3 = uvage.from_image(camera.x, camera.y, sheet[3])

# to animate, change which image is being used each time you draw
b3.image = sheet[4]
````

You can also load many individual image files instead if you want.




## Camera
A camera defines what is visible. It has a width, height, full screen status,
and can be moved. Moving a camera changes what is visible.

### Methods defined here:

#### clear
Usage: `camera.clear(color)`

Erases the screen by filling it with the given color

#### display
Usage: `camera.display()`

Causes what has been drawn recently by calls to draw(...) to be displayed on the screen

#### draw
Usage: `camera.draw(thing, *args)`

`camera.draw(box)` draws the provided SpriteBox object

`camera.draw(image, x, y)` draws the provided image centered at the provided coordinates

`camera.draw("Hi", 12, "red", x, y)` draws the text `Hi` in a red 12-point font at x,y

#### move
Usage: `camera.move(x, y=None)`

`camera.move(3, -7)` moves the screen's center to be 3 more pixels to the right and 7 more up

### Attributes defined here

#### Attributes you may set

The following attributes refer to the center of the viewable area:

-    `center`, which equals `(x, y)`
-    `x`
-    `y`

The following attributes refer to the edge of the viewable area:

-    `left`
-    `right`
-    `top`
-    `bottom`

The following attributes refer to a corner of the viewable area:

-    `topleft`
-    `topright`
-    `bottomleft`
-    `bottomright`

#### Attributes you may access but not set

The following refer to the size of the viewable area

-    `width`
-    `height`
-    `size`, which equals `(width, height)`

The following refer to the mouse cursor

-    `mousex`
-    `mousey`
-    `mouse`, which equals `(mousex, mousey)`
-    `mouseclick`, which is a Boolean value

#### User-defined attributes

You can add as many other attributes as you want, by (e.g.) saying `camera.number_of_coins_found = 5`.

##  SpriteBox
Intended to represent a sprite (i.e., an image that can be drawn as part of a larger view) and the box that contains it. Has various collision and movement methods built in.

SpriteBoxes are created using the functions `from_image`, `from_text`, or `from_color`.

### Methods defined here:

#### bottom_touches
Usage: `box.bottom_touches(other, padding=0, padding2=None)`

`b1.bottom_touches(b2)` returns `True` if both `b1.touches(b2)` and `b1`'s bottom edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### contains
Usage: `box.contains(x, y=None)`

checks if the given point is inside this SpriteBox's bounds or not

#### copy
Usage: `box.copy()`

Make a new SpriteBox just like this one and in the same location

#### copy_at
Usage: `box.copy_at(newx, newy)`

Make a new SpriteBox just like this one but at the given location instead of here

#### draw
Usage: `box.draw(surface)`

`b1.draw(camera)` is the same as saying `camera.draw(b1)`

`b1.draw(image)` draws a copy of `b1` on the image proivided

#### flip
Usage: `box.flip()`

mirrors the SpriteBox left-to-right. 

Mirroring top-to-bottom can be accomplished by

````python
b1.rotate(180)
b1.flip()
````

#### full_size
Usage: `box.full_size()`

change size of this SpriteBox to be the original size of the source image

#### left_touches
Usage: `box.left_touches(other, padding=0, padding2=None)`

`b1.left_touches(b2)` returns `True` if both `b1.touches(b2)` and `b1`'s left edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### move
Usage: `box.move(x, y=None)`

change position by the given amount in x and y. If only `x` given, assumed to be a point `[x, y]`

#### move_both_to_stop_overlapping
Usage: `box.move_both_to_stop_overlapping(other, padding=0, padding2=None)`

`b1.move_both_to_stop_overlapping(b2)` changes both `b1` and `b2`'s positions so that they no longer overlap.
See `touches` for a description of the optional padding arguments.

#### move_speed
Usage: `box.move_speed()`

change position by the current `speed` field of the SpriteBox object

#### move_to_stop_overlapping
Usage: `box.move_to_stop_overlapping(other, padding=0, padding2=None)`

`b1.move_to_stop_overlapping(b2)` makes the minimal change to `b1`'s position necessary so that they no longer overlap.
See `touches` for a description of the optional padding arguments.

#### overlap
Usage: `box.overlap( other, padding=0, padding2=None)`

`b1.overlap(b1)` returns a list of 2 values such that `self.move(result)` will cause them to not overlap
Returns `[0, 0]` if there is no overlap (i.e., if `b1.touches(b2)` returns `False`).

`b1.overlap(b2, 5)` adds a 5-pixel padding to `b1` before computing the overlap.
`b1.overlap(b2, 5, 10)` adds a 5-pixel padding in x and a 10-pixel padding in y before computing the overlap.

#### right_touches
Usage: `box.right_touches(other, padding=0, padding2=None)`

`b1.right_touches(b2)` returns `True` if both `b1.touches(b2)` and `b1`'s right edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### rotate
Usage: `box.rotate(angle)`

Rotates the SpriteBox by the given angle (in degrees).

#### scale_by
Usage: `box.scale_by(multiplier)`

Change the size of this SpriteBox by the given factor.
`b1.scale_by(1)` does nothing; 
`b1.scale_by(0.4)` makes b1 40% of its original width and height.

#### top_touches
Usage: `box.top_touches(other, padding=0, padding2=None)`

`b1.top_touches(b2)` returns `True` if both `b1.touches(b2)` and `b1`'s top edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### touches
Usage: `box.touches(other, padding=0, padding2=None)`

`b1.touches(b1)` returns `True` if the two SpriteBoxes overlap, `False` if they do not.
`b1.touches(b2, 5)` adds a 5-pixel padding to b1 before computing the touch.
`b1.touches(b2, 5, 10)` adds a 5-pixel padding in x and a 10-pixel padding in y before computing the touch.

### Attributes defined here

#### Attributes you may access and set


The following attributes refer to the center of the box:

-    `center`, which equals `(x, y)`
-    `x`
-    `y`

The following attributes refer to the edge of the box:

-    `left`
-    `right`
-    `top`
-    `bottom`

The following attributes refer to a corner of the box:

-    `topleft`
-    `topright`
-    `bottomleft`
-    `bottomright`

The following refer to the size of the box:

-    `width` -- setting this also changes `height` to keep the same aspect ratio.
-    `height` -- setting this also changes `width` to keep the same aspect ratio.
-    `size`, which equals `(width, height)`

The following attributes refer to the speed of the box:

-    `speed`, which equals `(speedx, speedy)`
-    `speedx`, also called `xspeed`
-    `speedy`, also called `yspeed`

The following attribute refers to the current look of the box:

-    `image`, representing the current look of the box.

#### Attributes you may access but not set

-    `rect`, providing the location and size of the box.

#### Attributes you may set but not access

-    `color`, replaces the current look of the box with a solid expanse of color

#### User-defined attributes

You can add as many other attributes as you want, by (e.g.) saying `box.number_of_coins_found = 5`.

# Functions

## from_color
Usage: `from_color(x, y, color, width, height)`

Creates a SpriteBox object at the given location with the given color, width, and height.

## from_image
Usage: `from_image(x, y, filename_or_url)`

Creates a SpriteBox object at the given location from the provided filename or url.

## from_text
Usage: `from_text(x, y, text, fontsize, color, bold=False, italic=False)`

Creates a SpriteBox object at the given location with the given text as its content.

## load_sprite_sheet
Usage: `load_sprite_sheet(url_or_filename, rows, columns)`

Loads a sprite sheet.
Assumes the sheet has rows-by-columns evenly-spaced images and returns a list of those images.


# More about keys and event loops

To only react to a key when it is first depressed, not as long as it is held down,
add `keys.clear()` to the end of your tick method.

````python
def tick():
    if uvage.is_pressing("up arrow"):
        print("the up arrow key was pressed")
    keys.clear() # makes uvage not report the same keys again
                 # until after they are released and re-pressed
    camera.display()

uvage.timer_loop(30, tick)
````

There is also a `keys_loop` function you can use instead of the `timer_loop` if your game does nothing at all (not even background animations) between two keys being typed.

````python
def click(keys):
    ifuvage.is_pressing("up arrow"):
        print("the up arrow key was pressed")
    camera.display()
# tell uvage to call the click method each time a key is pressed
uvage.keys_loop(click)
````

You can end your game by calling `uvage.stop_loop()`

````python
def tick(keys):
    if uvage.is_pressing("q"):
        uvage.stop_loop() # tick will not be called after this one call, ending the game
    camera.display()

uvage.timer_loop(30, tick)
````


## keys_loop
Usage: `keys_loop(callback)`

Requests that uvage call the provided function each time a key is pressed.

-    `callback`: a function that accepts the key pressed

````python
def onPress(key):
  if uvage.is_pressing("up arrow"):
      print 'up arrow pressed'
  if uvage.is_pressing("a"):
      print 'A key pressed'
  camera.draw(box)
  camera.display()

uvage.keys_loop(onPress)
````

You can *either* use `keys_loop` *or* use `timer_loop`, but not both.


## stop_loop
Usage: `stop_loop()`

Completely quits one `timer_loop` or `keys_loop`, usually ending the program.

## timer_loop
Usage: `timer_loop(fps, callback)`

Requests that uvage call the provided function fps times a second

-    `fps`: a number between 1 and 1000.  Note that numbers above 60 are likely to cause the program to repsond sluggishly.
-    `callback`: a function that accepts a set of keys pressed since the last tick.

````python
seconds = 0
def tick():
  seconds += 1/30
  if uvage.is_pressing("down arrow"):
      print 'down arrow pressed'
  camera.draw(box)
  camera.display()

uvage.timer_loop(30, tick)
````
## is_pressing
Usage: `is_pressing(key)`

Given a valid string containing the name of a computer key, returns `True` if `key` is currently being pressed and `False` otherwise. If the key provided is not a valid key, a KeyError will be raised. See the list below for all valid key names.
- backspace
- tab
- clear
- return
- pause
- escape
- space
- exclaim
- quotedbl
- hash
- dollar
- ampersand
- quote
- left parenthesis
- right parenthesis
- asterisk
- plus sign
- comma
- minus sign
- period
- forward slash
- 0
- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- colon
- semicolon
- less-than sign
- equals sign
- greater-than sign
- question mark
- at
- left bracket
- backslash
- right bracket
- caret
- underscore
- grave
- a
- b
- c
- d
- e
- f
- g
- h
- i
- j
- k
- l
- m
- n
- o
- p
- q
- r
- s
- t
- u
- v
- w
- x
- y
- z
- delete
- keypad 0
- keypad 1
- keypad 2
- keypad 3
- keypad 4
- keypad 5
- keypad 6
- keypad 7
- keypad 8
- keypad 9
- keypad period
- keypad divide
- keypad multiply
- keypad minus
- keypad plus
- keypad enter
- keypad equals
- up arrow
- down arrow
- right arrow
- left arrow
- insert
- home
- end
- page up
- page down
- F1
- F2
- F3
- F4
- F5
- F6
- F7
- F8
- F9
- F10
- F11
- F12
- F13
- F14
- F15
- numlock
- capslock
- scrollock
- right shift
- left shift
- right control
- left control
- right alt
- left alt
- right meta
- left meta
- left Windows key
- right Windows key
- mode shift
- help
- print screen
- sysrq
- break
- menu
- power
- Euro
- Android back button

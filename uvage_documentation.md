---
title: gamebox - summary overview
...

This page is a quick-start guide to [`gamebox.py`](files/gamebox.py).
Looking for something more definitive? See the [full documentation](gamebox.html).


# Necessary structure

Every PyGame program has three parts. It begins with this exact code:

````python
# beginning: must come first
import pygame
import gamebox
camera = gamebox.Camera(800, 600)
````

You can change the 800 and 600 to be the width and height of the window you want to make instead.

The middle will usually create various gameboxes to be used later. For example:

````python
# prep work: make the boxes and variables to be used later
logo = gamebox.from_image(0, 0,
    "https://www.python.org/static/img/python-logo.png")

score = 0
````

Every PyGame program should end with an event loop. There are a lot of pieces to making these work, so gamebox adds a simpler version:

````python
# make a method that will be called every frame. Must have parameter"keys"
def tick(keys):
    if pygame.K_UP in keys: # you can check which keys are being pressed
        print(" the up arrow key is currently being pressed")
    if camera.mouseclick: #true if some mouse button is being pressed
        logo.center = camera.mouse # the current mouse position
    camera.draw(logo)
    camera.display() # you almost always want to end this method with this line

# tell gamebox to call the tick method 30 times per second
gamebox.timer_loop(30, tick)
# this line of code will not be reached until after the window is closed
````

# gameboxes

A gamebox is an abstraction of an image or block of color inside a rectangle.
They can move, bump into one another, and change appearance.

There are four common ways to make a gamebox: from a color, an image, some text, or another gamebox.
All begin with the `x, y` of the center of the gamebox.

````python
b1 = gamebox.from_color(50, 100, "red", 20, 40) # x, y, color, width, height
b2 = gamebox.from_image(10, 30, "https://www.python.org/static/img/python-logo.png")
                       # x,  y,  url_or_filename
b3 = gamebox.from_text(50, 100, "Hi", 12, "red", italic=True)
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
camera = gamebox.Camera(width, height)
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
sheet = gamebox.load_sprite_sheet(
  "https://upload.wikimedia.org/wikipedia/commons/b/be/SpriteSheet.png",
  3, 4)

# make a gamebox image from one of those images
b3 = gamebox.from_image(camera.x, camera.y, sheet[3])

# to animate, change which image is being used each time you draw
b3.image = sheet[4]
````

You can also load many individual image files instead if you want.


# Other ideas

To only react to a key when it is first depressed, not as long as it is held down,
add `keys.clear()`{.python} to the end of your tick method.

````python
def tick(keys):
    if pygame.K_UP in keys:
        print("the up arrow key was pressed")
    keys.clear() # makes gamebox not report the same keys again
                 # until after they are released and re-pressed
    camera.display()

gamebox.timer_loop(30, tick)
````

There is also a `keys_loop` function you can use instead of the `timer_loop` if your game does nothing at all (not even background animations) between two keys being typed.

````python
def click(keys):
    if pygame.K_UP in keys:
        print("the up arrow key was pressed")
    camera.display()
# tell gamebox to call the click method each time a key is pressed
gamebox.keys_loop(click)
````

You can end your game by calling `gamebox.stop_loop()`

````python
def tick(keys):
    if pygame.K_q in keys:
        gamebox.stop_loop() # tick will not be called after this one call, ending the game
    camera.display()

gamebox.timer_loop(30, tick)
````

Looking for more? Try the [full gamebox documentation](gamebox.html).













---
title: Help on module gamebox
...


This page is the full documentation of [`gamebox.py`](files/gamebox.py), extracted from its docstrings.
Looking for a quick-start guide instead? See the [gamebox sumamry overview](gamebox-summary.html).

# Name

gamebox

# Description
This code is the original work of Luther Tychonievich, who releases it
into the public domain.

As a courtesy, Luther would appreciate it if you acknowledged him in any work
that benefited from this code.

# Classes

-    [Camera](#camera)
-    [SpriteBox](#spritebox)


## Camera
A camera defines what is visible. It has a width, height, full screen status,
and can be moved. Moving a camera changes what is visible.

### Methods defined here:

#### clear
Usage: `camera.clear(color)`{.python}

Erases the screen by filling it with the given color

#### display
Usage: `camera.display()`{.python}

Causes what has been drawn recently by calls to draw(...) to be displayed on the screen

#### draw
Usage: `camera.draw(thing, *args)`{.python}

`camera.draw(box)`{.python} draws the provided SpriteBox object

`camera.draw(image, x, y)`{.python} draws the provided image centered at the provided coordinates

`camera.draw("Hi", 12, "red", x, y)`{.python} draws the text `Hi` in a red 12-point font at x,y

#### move
Usage: `camera.move(x, y=None)`{.python}

`camera.move(3, -7)`{.python} moves the screen's center to be 3 more pixels to the right and 7 more up

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

You can add as many other attributes as you want, by (e.g.) saying `camera.number_of_coins_found = 5`{.python}.

##  SpriteBox
Intended to represent a sprite (i.e., an image that can be drawn as part of a larger view) and the box that contains it. Has various collision and movement methods built in.

SpriteBoxes are created using the functions `from_image`, `from_text`, or `from_color`.

### Methods defined here:

#### bottom_touches
Usage: `box.bottom_touches(other, padding=0, padding2=None)`{.python}

`b1.bottom_touches(b2)`{.python} returns `True`{.python} if both `b1.touches(b2)`{.python} and `b1`'s bottom edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### contains
Usage: `box.contains(x, y=None)`{.python}

checks if the given point is inside this SpriteBox's bounds or not

#### copy
Usage: `box.copy()`{.python}

Make a new SpriteBox just like this one and in the same location

#### copy_at
Usage: `box.copy_at(newx, newy)`{.python}

Make a new SpriteBox just like this one but at the given location instead of here

#### draw
Usage: `box.draw(surface)`{.python}

`b1.draw(camera)`{.python} is the same as saying `camera.draw(b1)`{.python}

`b1.draw(image)`{.python} draws a copy of `b1` on the image proivided

#### flip
Usage: `box.flip()`{.python}

mirrors the SpriteBox left-to-right. 

Mirroring top-to-bottom can be accomplished by

````python
b1.rotate(180)
b1.flip()
````

#### full_size
Usage: `box.full_size()`{.python}

change size of this SpriteBox to be the original size of the source image

#### left_touches
Usage: `box.left_touches(other, padding=0, padding2=None)`{.python}

`b1.left_touches(b2)`{.python} returns `True`{.python} if both `b1.touches(b2)`{.python} and `b1`'s left edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### move
Usage: `box.move(x, y=None)`{.python}

change position by the given amount in x and y. If only `x` given, assumed to be a point `[x, y]`

#### move_both_to_stop_overlapping
Usage: `box.move_both_to_stop_overlapping(other, padding=0, padding2=None)`{.python}

`b1.move_both_to_stop_overlapping(b2)`{.python} changes both `b1` and `b2`'s positions so that they no longer overlap.
See `touches` for a description of the optional padding arguments.

#### move_speed
Usage: `box.move_speed()`{.python}

change position by the current `speed` field of the SpriteBox object

#### move_to_stop_overlapping
Usage: `box.move_to_stop_overlapping(other, padding=0, padding2=None)`{.python}

`b1.move_to_stop_overlapping(b2)`{.python} makes the minimal change to `b1`'s position necessary so that they no longer overlap.
See `touches` for a description of the optional padding arguments.

#### overlap
Usage: `box.overlap( other, padding=0, padding2=None)`{.python}

`b1.overlap(b1)`{.python} returns a list of 2 values such that `self.move(result)`{.python} will cause them to not overlap
Returns `[0, 0]`{.python} if there is no overlap (i.e., if `b1.touches(b2)`{.python} returns `False`{.python}).

`b1.overlap(b2, 5)`{.python} adds a 5-pixel padding to `b1` before computing the overlap.
`b1.overlap(b2, 5, 10)`{.python} adds a 5-pixel padding in x and a 10-pixel padding in y before computing the overlap.

#### right_touches
Usage: `box.right_touches(other, padding=0, padding2=None)`{.python}

`b1.right_touches(b2)`{.python} returns `True`{.python} if both `b1.touches(b2)`{.python} and `b1`'s right edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### rotate
Usage: `box.rotate(angle)`{.python}

Rotates the SpriteBox by the given angle (in degrees).

#### scale_by
Usage: `box.scale_by(multiplier)`{.python}

Change the size of this SpriteBox by the given factor.
`b1.scale_by(1)`{.python} does nothing; 
`b1.scale_by(0.4)`{.python} makes b1 40% of its original width and height.

#### top_touches
Usage: `box.top_touches(other, padding=0, padding2=None)`{.python}

`b1.top_touches(b2)`{.python} returns `True`{.python} if both `b1.touches(b2)`{.python} and `b1`'s top edge is the one causing the overlap.
See `touches` for a description of the optional padding arguments.

#### touches
Usage: `box.touches(other, padding=0, padding2=None)`{.python}

`b1.touches(b1)`{.python} returns `True`{.python} if the two SpriteBoxes overlap, `False`{.python} if they do not.
`b1.touches(b2, 5)`{.python} adds a 5-pixel padding to b1 before computing the touch.
`b1.touches(b2, 5, 10)`{.python} adds a 5-pixel padding in x and a 10-pixel padding in y before computing the touch.

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

-    `image`, a [`pygame.Surface`](http://www.pygame.org/docs/ref/surface.html) representing the current look of the box.

#### Attributes you may access but not set

-    `rect`, a [`pygame.Rect`](http://www.pygame.org/docs/ref/rect.html) providing the location and size of the box.

#### Attributes you may set but not access

-    `color`, replaces the current look of the box with a solid expanse of color

#### User-defined attributes

You can add as many other attributes as you want, by (e.g.) saying `box.number_of_coins_found = 5`{.python}.

# Functions

## from_color
Usage: `from_color(x, y, color, width, height)`{.python}

Creates a SpriteBox object at the given location with the given color, width, and height.

## from_image
Usage: `from_image(x, y, filename_or_url)`{.python}

Creates a SpriteBox object at the given location from the provided filename or url.

## from_text
Usage: `from_text(x, y, text, fontsize, color, bold=False, italic=False)`{.python}

Creates a SpriteBox object at the given location with the given text as its content.

## keys_loop
Usage: `keys_loop(callback)`{.python}

Requests that pygame call the provided function each time a key is pressed.

-    `callback`: a function that accepts the key pressed

````python
def onPress(key):
  if pygame.K_DOWN == key:
      print 'down arrow pressed'
  if pygame.K_a in keys:
      print 'A key pressed'
  camera.draw(box)
  camera.display()

gamebox.keys_loop(onPress)
````

You can *either* use `keys_loop` *or* use `timer_loop`, but not both.

## load_sprite_sheet
Usage: `load_sprite_sheet(url_or_filename, rows, columns)`{.python}

Loads a sprite sheet.
Assumes the sheet has rows-by-columns evenly-spaced images and returns a list of those images.

## pause
Usage: `pause()`{.python}

Pauses the timer used to run the `timer_loop`; an error if there is no timer to pause.

## stop_loop
Usage: `stop_loop()`{.python}

Completely quits one `timer_loop` or `keys_loop`, usually ending the program.

## timer_loop
Usage: `timer_loop(fps, callback)`{.python}

Requests that pygame call the provided function fps times a second

-    `fps`: a number between 1 and 1000.  Note that numbers above 60 are likely to cause the program to repsond sluggishly.
-    `callback`: a function that accepts a set of keys pressed since the last tick.

````python
seconds = 0
def tick(keys):
  seconds += 1/30
  if pygame.K_DOWN in keys:
      print 'down arrow pressed'
  if not keys:
      print 'no keys were pressed since the last tick'
  camera.draw(box)
  camera.display()

gamebox.timer_loop(30, tick)
````

## unpause
Usage: `unpause()`{.python}

Unpauses the timer used to run the `timer_loop`; an error if there is no timer to unpause.

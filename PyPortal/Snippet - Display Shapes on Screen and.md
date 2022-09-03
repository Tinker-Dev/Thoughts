# Snippet - Display Shapes on Screen and Move Them

```
# Adapted from example at:
# https://github.com/ladyada/Adafruit_CircuitPython_Display_Shapes
import board
import displayio
import time
from adafruit_display_shapes.rect import Rect
from adafruit_display_shapes.circle import Circle
from adafruit_display_shapes.roundrect import RoundRect

#group = displayio.Group(max_size=10)  # Up to 10 shapes/tile grids
group = displayio.Group()  # Up to 10 shapes/tile grids

board.DISPLAY.show(group)

# Create a rectangle at (50, 50) with size of 10x25 pixels
# The default color is black, so it won't be
# visible against a black background
simple_rectangle = Rect(50, 50, 10, 25)

# Create a rectangle with all of the options
# Note that the outline fills inward, and does
# not affect the width/height of the shape
white_rectangle = Rect(
    x=0,
    y=0,
    width=25,
    height=25,
    fill=0xFFFFFF,  # Optionally fill the shape with a color
    outline=0xFF0000,  # Optionally outline with a color
    stroke=5,  # Thickness of the outline (default 1 pixel)
)

red_circle = Circle(
    x0=board.DISPLAY.width,  # Position of center of circle
    y0=board.DISPLAY.height,  # Position it in the bottom right
    r=20,
    fill=0xFF0000,
    outline=0xFFFFFF,
)

rounded_edge_purple_rectangle = RoundRect(
    x=50,
    y=100,
    width=100,
    height=30,
    r=15,  # Radius of the rounded corners
    fill=0x888888,
    outline=0xFFFFFF,
    stroke=3,
)

# Append shapes to the group, actually making them visible
group.append(simple_rectangle)  # Defaults to black so won't be visible
group.append(white_rectangle)
group.append(red_circle)
group.append(rounded_edge_purple_rectangle)

# Move one of the shapes around
# Changes will immediately be displayed
while True:
    rounded_edge_purple_rectangle.x += 5
    if rounded_edge_purple_rectangle.x > board.DISPLAY.width:
        rounded_edge_purple_rectangle.x = 0
    time.sleep(0.1)

```
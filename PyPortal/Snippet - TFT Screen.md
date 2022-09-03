# Snippet - TFT Screen

## Fade In:

```
import board
import time

i = 0
while i <= 1:
    board.DISPLAY.brightness = i
    time.sleep(.25)
    i += 0.1

```

  

## Resolution:

```
import board

print('Resolution: %sx%s' %
      (board.DISPLAY.width, board.DISPLAY.height))

```

  

## Moving Text:

```
import board
import terminalio
from adafruit_display_text import label
import time

# You must provide the text or the max_glyphs length, or both.
# If no max_glyphs specified, the maximum is set to length of text
# max_glyphs is the max amount of characters the text can contain
text_area = label.Label(
    terminalio.FONT,
    text="PyPortal\nRocks",
    max_glyphs=50,  # Optionally allow longer text to be added
    color=0xFFFF00,
    x=20,  # Pixel offsets from (0, 0) the top left
    y=20,
    line_spacing=1,  # Distance between lines
)

board.DISPLAY.show(text_area)

# You can modify the x and y coordinates and it will
# immediately update the position
for i in range(0, 50, 10):
    text_area.y += i
    text_area.x += i
    time.sleep(.5)

# Change the text color
text_area.color = 0xFF0000
# Add to text
text_area.text = text_area.text + '!!1'

# Keep the program running, otherwise the display is cleared
while True:
    time.sleep(300)

```

  

## Display Text:

```
import board
from adafruit_bitmap_font import bitmap_font
from adafruit_display_text import label
import time

custom_font = bitmap_font.load_font("fonts/Arial-ItalicMT-17.bdf")

text_area = label.Label(
    custom_font,
    text="PyPortal Rocks",
    color=0x00FFFF,
    x=50,  # Pixel offsets from (0, 0) the top left
    y=50,
    line_spacing=1,  # Distance between lines
)

board.DISPLAY.show(text_area)

# Keep program running so text remains displayed
while True:
    time.sleep(300)

```

  

## Display and Move Image:

```
import board
import displayio
import time

image_file = open("pyportal_startup.bmp", "rb")
bitmap_contents = displayio.OnDiskBitmap(image_file)

tile_grid = displayio.TileGrid(
    bitmap_contents,
    pixel_shader=displayio.ColorConverter(),
    default_tile=0,
    x=0,  # Position relative to its parent group
    y=0,
    width=1,  # Number of tiles in the grid
    height=1,
    # tile_width=None,  # Number of tiles * tile size must match BMP size
    # tile_height=None,  # None means auto size the tiles
)

group = displayio.Group()
group.append(tile_grid)
board.DISPLAY.show(group)

# Move the whole group (which includes the TileGrid that has our image)
# The TileGrids inside the group have a relative position to the
# position of the group.
for i in range(0, 25, 5):
    group.x = i
    group.y = i
    time.sleep(.1)
# Then reset it back to 0,0
group.x, group.y = 0, 0

# You can scale groups by integer values, default is 1
group.scale = 2

# Each TileGrid inside a group has its own position relative to
# the position of the parent group.
# Move the TileGrid only, leaving group in same spot
for i in range(0, 25, 5):
    tile_grid.x = i
    tile_grid.y = i
    time.sleep(.1)

# If you had more TileGrids, you could add or remove them with:
# group.append()
# group.pop()
# group.insert()

# If you close the file, it will not be able to display any more
# image_file.close()

# Keep program running so image stays up
while True:
    time.sleep(300)

```
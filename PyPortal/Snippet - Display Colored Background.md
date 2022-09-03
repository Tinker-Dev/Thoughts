# Snippet - Display Colored Background

```
import board
import displayio
import time

number_of_colors = 3

palette = displayio.Palette(number_of_colors)  # Palette with 3 colors max
palette[0] = 0xFF0000  # Red
palette[1] = 0xFFFFFF  # White
palette[2] = 0x0000FF  # Blue

bitmap = displayio.Bitmap(
    board.DISPLAY.width,
    board.DISPLAY.height,
    number_of_colors,
)

# Now that we have a palette and a bitmap ready, we can create and use
# a TileGrid just like the previous example.
# The entire bitmap will be filled with palette[0] color on initialization
tile_grid = displayio.TileGrid(bitmap, pixel_shader=palette)

group = displayio.Group()
group.append(tile_grid)

board.DISPLAY.show(group)

# At this point the bitmap is being displayed and we can manipulate it
# When assigning a bitmap pixel value, you don't specify an RGB color
# you specify the palette index which already has an RGB color assigned.
# In this case, 0 = Red, 1 = White, and 2 = Blue as defined in the palette.
bitmap[0, 0] = 1  # Top left pixel
bitmap[319, 239] = 1  # Bottom right pixel
bitmap[319, 0] = 1  # Top right pixel
bitmap[0, 239] = 1  # Bottom left pixel
bitmap[160, 120] = 2  # Center pixel

# Keep program running so our bitmap remains visible
while True:
    time.sleep(300)

```
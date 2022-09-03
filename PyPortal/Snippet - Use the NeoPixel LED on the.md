# Snippet - Use the NeoPixel LED on the Back

```
import board
import neopixel
import time

# Connect to the NeoPixel (there is only one so it is index 0)
# auto_write means we don't have to call pixels.show() each time
pixels = neopixel.NeoPixel(board.NEOPIXEL, 1, auto_write=True)

while True:
    pixels[0] = (255, 0, 0)  # Red, green, blue
    time.sleep(.25)
    pixels[0] = (0, 0, 255)
    time.sleep(.25)

```
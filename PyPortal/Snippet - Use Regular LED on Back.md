# Snippet - Use Regular LED on Back

```
import digitalio
import board
import time

led = digitalio.DigitalInOut(board.L)  # Or board.D13
led.direction = digitalio.Direction.OUTPUT

while True:
    led.value = True
    time.sleep(1)
    led.value = False
    time.sleep(1)

```
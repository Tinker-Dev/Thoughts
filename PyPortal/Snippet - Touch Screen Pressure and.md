# Snippet - Touch Screen, Pressure and Location

```
# Adapted from example at:
# https://circuitpython.readthedocs.io/projects/touchscreen/en/latest/examples.html
import board
import adafruit_touchscreen

touchscreen = adafruit_touchscreen.Touchscreen(
    board.TOUCH_XL,
    board.TOUCH_XR,
    board.TOUCH_YD,
    board.TOUCH_YU,
    # calibration=((9000, 59000), (8000, 57000)),
    # resistance=None
    size=(board.DISPLAY.width, board.DISPLAY.height),
)

while True:
    point_being_touched = touchscreen.touch_point
    if point_being_touched:
        print(
            'Touch at (%s, %s) Pressure: %s' %
            (point_being_touched[0],
             point_being_touched[1],
             point_being_touched[2]),
        )

```
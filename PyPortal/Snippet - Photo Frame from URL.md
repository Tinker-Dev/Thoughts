# Snippet - Photo Frame from URL

## Version 1:

You will need a local image to start with.

```
# SPDX-FileCopyrightText: 2019 Limor Fried for Adafruit Industries
#
# SPDX-License-Identifier: MIT


import time
import board
from adafruit_pyportal import PyPortal


# Set up where we'll be fetching data from, we have a few different
# cute animal services for cats, dogs and foxes!


# random cat
DATA_SOURCE = "https://api.thecatapi.com/v1/images/search"
IMAGE_LOCATION = [0, "url"]


# random fox
#DATA_SOURCE = "https://randomfox.ca/floof/"
#IMAGE_LOCATION = ["image"]


# random shibe
#DATA_SOURCE = "http://shibe.online/api/shibes?count=1"
#IMAGE_LOCATION = [0]


# determine the current working directory needed so we know where to find files
cwd = ("/"+__file__).rsplit('/', 1)[0]
pyportal = PyPortal(url=DATA_SOURCE,
                    status_neopixel=board.NEOPIXEL,
                    default_bg=cwd+"/blackscreen.bmp",
                    image_json_path=IMAGE_LOCATION,
                    image_resize=(320, 240),
                    image_position=(0, 0))


while True:
    response = None
    try:
        response = pyportal.fetch()
        print("Response is", response)
    except RuntimeError as e:
        print("Some error occured, retrying! -", e)
        continue


    stamp = time.monotonic()
    # wait 5 minutes before getting again
    while (time.monotonic() - stamp) < (5*60):
        # or, if they touch the screen, fetch immediately!
        if pyportal.touchscreen.touch_point:
            break


```

  

  

## Version 2:

  

```
# SPDX-FileCopyrightText: 2020 Dan Cogliano for Adafruit Industries
#
# SPDX-License-Identifier: MIT


import time
import random
import board
from adafruit_pyportal import PyPortal
from adafruit_display_shapes.circle import Circle


WIDTH = board.DISPLAY.width
HEIGHT = board.DISPLAY.height


#pylint: disable=line-too-long


# these lines show the entire collection
APIURL = "https://openaccess-api.clevelandart.org/api/artworks?cc0=1&has_image=1&indent=2&limit=1&skip="
IMAGECOUNT = 31954


# uncomment these lines to show just paintings
# APIURL = "https://openaccess-api.clevelandart.org/api/artworks?cc0=1&has_image=1&indent=2&limit=1&type=Painting&skip="
# IMAGECOUNT = 3223


BACKGROUND_FILE = "/background.bmp"
if WIDTH > 320:
    BACKGROUND_FILE = "/background_480.bmp"


pyportal = PyPortal(default_bg=BACKGROUND_FILE,
                    image_json_path=["data", 0, "images", "web", "url"],
                    image_dim_json_path=(["data", 0, "images", "web", "width"],
                                         ["data", 0, "images", "web", "height"]),
                    image_resize=(WIDTH, HEIGHT - 15),
                    image_position=(0, 0),
                    text_font="/fonts/OpenSans-9.bdf",
                    json_path=["data", 0, "title"],
                    text_position=(4, HEIGHT - 9),
                    text_color=0xFFFFFF)


circle = Circle(WIDTH - 8, HEIGHT - 7, 5, fill=0)
pyportal.splash.append(circle)
loopcount = 0
errorcount = 0
while True:
    response = None
    try:
        circle.fill = 0xFF0000
        itemid = random.randint(1, IMAGECOUNT)
        # itemid = 20 # portrait mode example
        # itemid = 21 # landscape mode example
        print("retrieving url:", APIURL + str(itemid))
        response = pyportal.fetch(APIURL + str(itemid))
        circle.fill = 0
        print("Response is", response)
        loopcount = loopcount + 1


    except (RuntimeError, KeyError, TypeError) as e:
        print("An error occured, retrying! -", e)
        print("loop counter:", loopcount)
        assert errorcount < 20, "Too many errors, stopping"
        errorcount = errorcount + 1
        time.sleep(60)
        continue


    errorcount = 0
    stamp = time.monotonic()
    # wait 5 minutes before getting again
    while (time.monotonic() - stamp) < (0.1*60):
        # or, if they touch the screen, fetch immediately!
        if pyportal.touchscreen.touch_point:
            break


```
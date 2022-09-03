# Libraries

## Where do you get updated libraries?

You can download the libraries that match your version of CircuitPython here.

- [https://circuitpython.org/libraries](https://circuitpython.org/libraries)  
    

  

## Which libraries do you need?

### Easy/Noob Method:

- When you are first starting off it may be best to copy the whole `lib` folder to your `CIRCUITPY` drive.
- This method does eat up a lot of your 8mb drive, but it may not be an issue.

  

### More restricted Method:

- `adafruit_esp32spi` - This is the library that gives you internet access via the ESP32 using (you guessed it!) SPI transport. You need this for anything Internet
- `adafruit_requests` - This library allows us to perform HTTP requests and get responses back from servers. GET/POST/PUT/PATCH - they're all in here!
- `adafruit_pyportal` - This is our friendly wrapper library that does a lot of our projects, displays graphics and text, fetches data from the internet. Nearly all of our projects depend on it!
- `adafruit_portalbase` - This library is the base library that adafruit\_pyportal library is built on top of.
- `adafruit_touchscreen` - a library for reading touches from the resistive touchscreen. Handles all the analog noodling, rotation and calibration for you.
- `adafruit_io` - this library helps connect the PyPortal to our free datalogging and viewing service
- `adafruit_imageload` - an image display helper, required for any graphics!
- `adafruit_display_text` - not surprisingly, it displays text on the screen
- `adafruit_bitmap_font` - we have fancy font support, and its easy to make new fonts. This library reads and parses font files.
- `adafruit_slideshow` - for making image slideshows - handy for quick display of graphics and sound
- `neopixel` - for controlling the onboard neopixel
- `adafruit_adt7410` - library to read the temperature from the on-board Analog Devices ADT7410 precision temperature sensor (not necessary for Titano or Pynt)
- `adafruit_sdcard` - support for reading/writing data from the onboard SD card slot.
- `adafruit_bus_device` - low level support for I2C/SPI
- `adafruit_fakerequests` - This library allows you to create fake HTTP requests by using local files.
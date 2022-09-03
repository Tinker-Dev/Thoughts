# Snippet - Read Temp at Board

```
import board
import busio
import adafruit_adt7410
import time


temp_sensor = adafruit_adt7410.ADT7410(
    busio.I2C(board.SCL, board.SDA),
    address=0x48,  # Specific device address for ADT7410
)
temp_sensor.high_resolution = True



degrees_celsius = temp_sensor.temperature
degrees_fahrenheit  = (temp_sensor.temperature * 1.8000) + 32.00


print('Temperature: %s C' % degrees_celsius)
print('Temperature: %s F' % degrees_fahrenheit)


```
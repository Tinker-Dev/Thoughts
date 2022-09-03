# Snippet - Read Light Sensor Value

```
import board
import analogio
import time


light_sensor = analogio.AnalogIn(board.LIGHT)


while True:
    # Get analog value from light sensor
    # Range will be 0 - 65535
    light_value = light_sensor.value
    print('Light sensor value: %s' % light_value)
    time.sleep(1)
```
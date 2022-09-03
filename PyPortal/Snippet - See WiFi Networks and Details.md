# Snippet - See WiFi Networks and Details

```
import board
from digitalio import DigitalInOut
import busio
from adafruit_esp32spi import adafruit_esp32spi

esp32_cs_pin = DigitalInOut(board.ESP_CS)
esp32_ready_pin = DigitalInOut(board.ESP_BUSY)
esp32_reset_pin = DigitalInOut(board.ESP_RESET)

spi = busio.SPI(board.SCK, board.MOSI, board.MISO)

esp = adafruit_esp32spi.ESP_SPIcontrol(
    spi,
    esp32_cs_pin,
    esp32_ready_pin,
    esp32_reset_pin,
)

if esp.status == adafruit_esp32spi.WL_IDLE_STATUS:
    print('ESP32 found and in idle mode')

print("ESP32 MAC address: %s" %
      ':'.join(['{:02X}'.format(byte) for byte in esp.MAC_address]))

print('Access points detected:')
print(' - SSID | Strength | Encryption')
for ap in esp.scan_networks():
    print("- %s | %s | %s" %
          (str(ap['ssid'], 'utf8'), ap['rssi'], ap['encryption'])
    )

```
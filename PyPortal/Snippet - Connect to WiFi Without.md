# Snippet - Connect to WiFi Without Secrets

## Connect and get details about the connection:

```
import board
from digitalio import DigitalInOut
import busio
from adafruit_esp32spi import adafruit_esp32spi

SSID = 'wifi_network_name'
SSID_PASSWORD = 'wifi_password'

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

print('Connecting to access point: %s' % str(SSID, 'utf8'))
while not esp.is_connected:
    try:
        esp.connect_AP(SSID, SSID_PASSWORD)
    except RuntimeError as e:
        print(e)
        continue

# Get network information
print('Connected to %s' % str(esp.ssid, 'utf-8'))
print('Signal strength: %s' % esp.rssi)
print('ESP32 IP address: %s' % esp.pretty_ip(esp.ip_address))
print("ESP32 MAC address: %s" %
      ':'.join(['{:02X}'.format(byte) for byte in esp.MAC_address]))

# At this point, you are connected and can do things like
# ping, do DNS lookups, and make HTTP requests

```

  

## Connect and pull DNS and Ping of site:

```
import board
from digitalio import DigitalInOut
import busio
from adafruit_esp32spi import adafruit_esp32spi


SSID = 'wifi_network_name'
SSID_PASSWORD = 'wifi_password'

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

print('Connecting to access point: %s' % str(SSID, 'utf8'))
while not esp.is_connected:
    try:
        esp.connect_AP(SSID, SSID_PASSWORD)
    except RuntimeError as e:
        print(e)
        continue

# At this point, you are connected and can do things like
# ping, do DNS lookups, and make HTTP requests

print("DNS lookup www.devdungeon.com: %s" %
      esp.pretty_ip(esp.get_host_by_name("www.devdungeon.com")))

for i in range(3):
    print("Ping www.devdungeon.com: %d ms" %
          esp.ping("www.devdungeon.com"))

```
# Snippet - Read From SD Card

```
import os
import board
import sdcardio
import storage

sd = sdcardio.SDCard(board.SPI(), board.SD_CS)
vfs = storage.VfsFat(sd)
storage.mount(vfs, '/sd')
files_on_sd = os.listdir('/sd')

print(files_on_sd)
```
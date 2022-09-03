# Snippet - Use the Speaker

Neither of this worked. They may need a tweak.

###   

### Play File:

  

```
import audioio
import board
import time

def play_sound_file(file_path):
    try:
        with open(file_path, "rb") as f:
            wave = audioio.WaveFile(f)
            audio.play(wave)
            while audio.playing:
                time.sleep(0.005)
    except OSError as e:
        print('Error opening file: %s' % e)

with audioio.AudioOut(board.AUDIO_OUT) as audio:  # or board.A0
    play_sound_file("pyportal_startup.wav")

```

  

  

Play Sine Wave:

```
# Adapted from examples at:
# https://circuitpython.readthedocs.io/en/latest/shared-bindings/audioio/AudioOut.html
import time
import array
import math
import audioio
import board

SAMPLERATE = 8000  # 8000 samples per second

def generate_sine_wave(frequency=440):
    length = SAMPLERATE // frequency
    sine_wave_data = array.array("H", [0] * length)
    for i in range(length):
        sine_wave_data[i] = int(math.sin(math.pi*2*i/18) * (2**15) + 2**15)
    sound_sample = audioio.RawSample(sine_wave_data)
    return sound_sample

sample = generate_sine_wave()
audio = audioio.AudioOut(board.AUDIO_OUT)  # or board.A0
audio.play(sample, loop=True)

while True:  # Stay running
    time.sleep(300)

```
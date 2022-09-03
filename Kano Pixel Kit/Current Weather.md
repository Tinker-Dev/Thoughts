# Current Weather

This is out of date as DarkSky was bought by Apple and they discontiued the API.

  

```
#
# Powered by Dark Sky. https://darksky.net/poweredby/ 
# Thanks for DarkSky for providing a wonderful API.
#

# Bringing in all the modules that we need.
import PixelKit as kit
import urequests
import ujson
import usocket

display_temp = ' '
display_sky = ' '

letters = {
  ' ': [
  	[0, 0, 0],
    [0, 0, 0],
    [0, 0, 0],
    [0, 0, 0],
    [0, 0, 0]
  ],
  '1': [
  	[0, 1, 0],
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0]
  ],
  '2': [
  	[1, 1, 1],
    [0, 0, 1],
    [1, 1, 1],
    [1, 0, 0],
    [1, 1, 1]
  ],
  '3': [
  	[1, 1, 1],
    [0, 0, 1],
    [1, 1, 1],
    [0, 0, 1],
    [1, 1, 1]
  ],
  '4': [
  	[1, 0, 1],
    [1, 0, 1],
    [1, 1, 1],
    [0, 0, 1],
    [0, 0, 1]
  ],
  '5': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 1, 1],
    [0, 0, 1],
    [1, 1, 1]
  ],
  '6': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 1, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  '7': [
  	[1, 1, 1],
    [0, 0, 1],
    [0, 0, 1],
    [0, 1, 0],
    [0, 1, 0]
  ],
  '8': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 1, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  '9': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 1, 1],
    [0, 0, 1],
    [1, 1, 1]
  ],
  '0': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  '^': [
  	[1, 1, 0],
    [1, 1, 0],
    [0, 0, 0],
    [0, 0, 0],
    [0, 0, 0]
  ],
  '-': [
  	[0, 0, 0],
    [0, 0, 0],
    [1, 1, 1],
    [0, 0, 0],
    [0, 0, 0]
  ],
  'a': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 1, 1],
    [1, 0, 1],
    [1, 0, 1]
  ],
  'b': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 1, 0],
    [1, 0, 1],
    [1, 1, 1]
  ],
  'c': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 0, 0],
    [1, 0, 0],
    [1, 1, 1]
  ],
  'd': [
  	[1, 1, 0],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 0]
  ],
  'e': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 1, 0],
    [1, 0, 0],
    [1, 1, 1]
  ],
  'f': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 1, 0],
    [1, 0, 0],
    [1, 0, 0]
  ],
  'g': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  'h': [
  	[1, 0, 1],
    [1, 0, 1],
    [1, 1, 1],
    [1, 0, 1],
    [1, 0, 1]
  ],
  'i': [
  	[1, 1, 1],
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0],
    [1, 1, 1]
  ],
  'j': [
  	[0, 1, 1],
    [0, 0, 1],
    [0, 0, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  'k': [
  	[1, 0, 1],
    [1, 0, 1],
    [1, 1, 0],
    [1, 0, 1],
    [1, 0, 1]
  ],
  'l': [
  	[1, 0, 0],
    [1, 0, 0],
    [1, 0, 0],
    [1, 0, 0],
    [1, 1, 1]
  ],
  'm': [
  	[1, 0, 1],
    [1, 1, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1]
  ],
  'n': [
  	[1, 1, 0],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1]
  ],
  'o': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  'p': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 1, 1],
    [1, 0, 0],
    [1, 0, 0]
  ],
  'q': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 1],
    [0, 0, 1]
  ],
  'r': [
  	[1, 1, 1],
    [1, 0, 1],
    [1, 1, 0],
    [1, 0, 1],
    [1, 0, 1]
  ],
  's': [
  	[1, 1, 1],
    [1, 0, 0],
    [1, 1, 1],
    [0, 0, 1],
    [1, 1, 1]
  ],
  't': [
  	[1, 1, 1],
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0]
  ],
  'u': [
  	[1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 1]
  ],
  'v': [
  	[1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [0, 1, 0]
  ],
  'w': [
  	[1, 0, 1],
    [1, 0, 1],
    [1, 0, 1],
    [1, 1, 1],
    [1, 0, 1]
  ],
  'x': [
  	[1, 0, 1],
    [1, 0, 1],
    [0, 1, 0],
    [1, 0, 1],
    [1, 0, 1]   
  ],
  'y': [
    [1, 0, 1],
    [1, 0, 1],
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0]  
  ],
  'z': [
  	[1, 1, 1],
    [0, 0, 1],
    [0, 1, 0],
    [1, 0, 0],
    [1, 1, 1]
  ],
  '!': [
  	[0, 1, 0],
    [0, 1, 0],
    [0, 1, 0],
    [0, 0, 0],
    [0, 1, 0]
  ]
}

def keep_alive():
  usocket.getaddrinfo('google.com',1)[0][-1][0]
  

def weather():
  # The address for which we are pulling data from.
  # 1000 calls per day limit.
  url = 'https://api.darksky.net/forecast/XXXXXXXXXXX API KEY XXXXXXXXXXX/YOUR_LAT,YOUR_LONG?exclude=[flags,alerts,daily,hourly,minutely]'

  # Pulling the data from the site.
  response = urequests.get(url)

  # Parsing the data that is coming it.
  array = response.json()
  
  # The data has a formating issue so we dump the dictionary 
  # it to string first to clear this up.
  dump = ujson.dumps(array)

  # Taking the string back to a dictionary so that we
  # can use the key value pairing.
  newdict = ujson.loads(dump)

  # Everything that we need is in a sub dictionary. This
  # is pulling that data and assigning it.
  subdict = newdict['currently']
  #current_temp = subdict['temperature']

  # Locating the items we want from the sub dictionary.
  #print(subdict['icon'])
  #print(int(subdict['temperature']))
  current_temp = subdict['temperature']
  current_sky = subdict['icon']
  #print (current_temp)
  
  return [current_temp,current_sky]


def draw_letter(x, y, letter, red, green, blue):
    for sy, row in enumerate(letter):
      for sx, value in enumerate(row):
        posx = x+sx
        posy = y+sy
        if value != 0 and \
           posx >= 0 and posx <= 15 and \
           posy >= 0 and posy <= 7:
          kit.set_pixel(x+sx, y+sy, [red, green, blue])

def draw_word(x, y, word, red, green, blue):
	for i in range(0, len(word)):
	  letter = word[i]
	  sprite = letters[letter]
	  draw_letter(x + (i*4), y, sprite, red, green, blue)

def scroll(word, red, green, blue, speed=1):
  for i in range(0,16 + (len(word)*4)):
    kit.set_background([0, 0, 0])
    draw_word(16-i, 1, word, red, green, blue)
    kit.render()
    sleep(1/speed)

run_count = 1    
stop_count = 1
while stop_count < 10:
  run_count = run_count + 1
  #red = random.randrange(3, 5)
  #green = random.randrange(3, 5)
  #blue = random.randrange(3, 5)
  if run_count == 2:
    weather_values = weather()
    display_temp = str(int(weather_values[0]))
    display_sky = weather_values[1].lower().replace('-',' ').replace('night','').replace('day','')
      
  red = 5
  green = 5
  blue = 5
  #print(phrase)
  scroll(display_temp + '^ ' + display_sky, red, green, blue, 15)
  if run_count == 30:
    run_count = 1
  #print(weather_values)
  #print(display_temp)
  #print(display_sky)
  
  
  #stop_count = stop_count + 1
  
  #print(stop_count)
  #print(run_count)
  keep_alive()

```
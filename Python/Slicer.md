# Slicer

```
# Cuts a string of data at index locations.
def Slicer(def_row, def_slicepoints):
    newline = []
    position = 0

    slicepoints = def_slicepoints.split(',')

    cuts = len(slicepoints)
    while cuts > 0:
        for sp in slicepoints:
            length = int(sp.strip())
            value = def_row[position:position + length]
            newline.append(value.strip())
            position = position + length
            cuts = cuts - 1

    return newline

```
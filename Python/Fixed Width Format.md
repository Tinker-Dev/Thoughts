# Fixed Width Format

```
class FixWidthFieldLine(object):

    fields = (('foo', 10),
              ('bar', 30),
              ('ooga', 30),
              ('booga', 10))

    def __init__(self):
        self.foo = ''
        self.bar = ''
        self.ooga = ''
        self.booga = ''

    def __str__(self):
        return ''.join([getattr(self, field_name).ljust(width) 
                        for field_name, width in self.fields])

f = FixWidthFieldLine()
f.foo = 'hi'
f.bar = 'joe'
f.ooga = 'howya'
f.booga = 'doin?'

print (f)

```
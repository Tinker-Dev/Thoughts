# Message Box

This uses the GUI module that is built into Python. You don’t have to acquire anything extra to do this.  

  

```

import tkinter as tk
from tkinter import messagebox

DummyWindow = tk.Tk()
DummyWindow.withdraw()
messagebox.showerror(“Window Title Here”,”Message Body Here”)

```

  

- The “DummyWindow” lines are there to deal with a inert window that gets opened and not used.
- The “showerror” part of the messagebox line is what sets the icon the box uses. There are other values that can go he
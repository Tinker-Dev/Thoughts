# Message Manager

This is more to simplify the process of creating a messages when you don't normally have a UI.  

  

```
################################# Load Modules #################################
import tkinter as tk
from tkinter import messagebox

################################# Function #################################

# Generates a Messages Boxes
def show_messsage(def_type, def_title, def_message):
    # Setting a fake main window and then hiding it.
    # If this is not done a blank parent window will open
    # with the message window.
    root = tk.Tk()
    root.withdraw()

    # Defining the message types that we will use.
    if def_type == 'info':
        messagebox.showinfo(def_title, def_message)
    elif def_type == 'error':
        messagebox.showerror(def_title, def_message)
    else:
        pass

```
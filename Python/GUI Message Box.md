# GUI Message Box

When you need to display a message to a person and, you want to make sure that it gets their attention, you can use a Message Box. Here is the code to do that.  

  

```
# Creating the message GUIs.
import tkinter as tk
from tkinter import messagebox

# tkinter opens a parent window before it opens the message
# box. This hids that parent window as it isn't needed.
guiframe = tk.Tk()
guiframe.withdraw()

# There are several types of messages boxes. Here are some examples.
# These types change the embeded icon and the button options.
# These will just close on button click until you get to the last
# box. The last message box with show you what to do with the
# button clicks.
messagebox.showinfo("Message Box",'This is a sample "showinfo" message box.')

messagebox.showerror("Message Box",'This is a sample "showerror" message box.')

messagebox.showwarning("Message Box",'This is a sample "showwarning" message box.')

messagebox.askokcancel("Message Box",'This is a sample "askokcancel" message box.')

messagebox.askyesno("Message Box",'This is a sample "askyesno" message box.')

messagebox.askretrycancel("Message Box",'This is a sample "askretrycancel" message box.')

# This displays and box with "Yes" and "No" depending on what
# pick you will got the happy or sad path.
MsgBox = tk.messagebox.askquestion ('What are your thoughts?','Did you learn anything?')
if MsgBox == 'yes':
    messagebox.showinfo("Happy Box",'That is great!')
    guiframe.destroy()
else:
    messagebox.showerror("Sad Box",'That is too bad!')
    guiframe.destroy()

```
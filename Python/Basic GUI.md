# Basic GUI

I have been told that “Engines” use combustion and “Motors” use electricity. One could use the same idea with “Application” and “Program”.

- Application’s have a GUI.
- Programs are just run able code.

  

If that is the case, then you might want this to get started.  

  

```
# Import the GUI Module
import tkinter as tk
import datetime

#Setting Global Variables
global entered_value1
global entered_value2
global entered_value3
global entered_value4
global entered_value5
global entered_value6


# The Main Items of the Script
class PrimaryFrame():
    # Define the Main Window
    def __init__(self):
        self.window = tk.Tk()
        self.window.title("Questionnaire")
        self.inner_widgets()
    
    # Define the main objects of the Main Window    
    def inner_widgets(self):
        # Create some room around all the internal frames
        self.window['padx'] = 5
        self.window['pady'] = 5
        
        
        # Defining Output Action
        # This is printing to the console, but there are
        # tons of options for here.
        def actions():
            # Use the Data
            if len(entered_value1.get()) > 0:
                print(entered_value1.get())
            if len(entered_value2.get()) > 0:
                print(entered_value2.get())
            if len(entered_value3.get()) > 0:     
                print(entered_value3.get()) 
            if len(entered_value4.get()) > 0:
                print(entered_value4.get())
            if len(entered_value5.get()) > 0:
                print(entered_value5.get())
            if len(entered_value6.get()) > 0:
                print(entered_value6.get())
            
            now = datetime.datetime.now()    
            print(now.isoformat())
           
            # Clear the Screen
            entered_value1.delete(0, 'end')
            entered_value2.delete(0, 'end')
            entered_value3.delete(0, 'end')
            entered_value4.delete(0, 'end')
            entered_value5.delete(0, 'end')
            entered_value6.delete(0, 'end')            
        
        # Creating First Section
        first_frame = tk.LabelFrame(self.window, text="Demographics", relief=tk.RIDGE)
        first_frame.grid(row=0, column=0, sticky=tk.E + tk.W + tk.N + tk.S, padx=10, pady=10) 
        
        tk.Label(first_frame, text = "Name:").grid(sticky="W", row = 0, column = 0, padx=10)
        entered_value1 = tk.Entry(first_frame, width=40)
        entered_value1.grid(row = 0, column = 1, padx=10, pady=10, sticky="W")
                
        tk.Label(first_frame, text = "Phone:").grid(sticky="W", row = 1, column = 0, padx=10, pady=10)
        entered_value2 = tk.Entry(first_frame, width=15)
        entered_value2.grid(row = 1, column = 1, padx=10, pady=10, sticky="W")
        
        tk.Label(first_frame, text = "Email:").grid(sticky="W", row = 2, column = 0, padx=10, pady=10)
        entered_value3 = tk.Entry(first_frame,width=35)
        entered_value3.grid(row = 2, column = 1, padx=10, pady=10, sticky="W")
        
    
        # Create Second Section
        second_frame = tk.LabelFrame(self.window, text="Social", relief=tk.RIDGE)
        second_frame.grid(row=1, column=0, sticky=tk.E + tk.W + tk.N + tk.S, padx=10, pady=10)
        
        tk.Label(second_frame, text = "Facebook:").grid(sticky="W", row = 0, column = 0, padx=10)
        entered_value4 = tk.Entry(second_frame, width=40)
        entered_value4.grid(row = 0, column = 1, padx=10, pady=10, sticky="W")
        
        tk.Label(second_frame, text = "YouTube:").grid(sticky="W", row = 1, column = 0, padx=10)
        entered_value5 = tk.Entry(second_frame, width=40)
        entered_value5.grid(row = 1, column = 1, padx=10, pady=10, sticky="W")


        # Create Third Section
        third_frame = tk.LabelFrame(self.window, text="Website", relief=tk.RIDGE)
        third_frame.grid(row=2, column=0, sticky=tk.E + tk.W + tk.N + tk.S, padx=10, pady=10)
        
        tk.Label(third_frame, text = "URL:").grid(sticky="W", row = 0, column = 0, padx=10)
        entered_value6 = tk.Entry(third_frame, width=40)
        entered_value6.grid(row = 0, column = 1, padx=10, pady=10, sticky="W")

        
        # Buttons to Take Action
        submit_button = tk.Button(self.window, text="Submit",command=actions,padx=5)
        submit_button.grid(row=5, column=0,sticky="E",padx=10, pady=10)
        quit_button = tk.Button(self.window, text="Done", command=self.window.destroy, padx=12)
        quit_button.grid(row=5, column=0,sticky="W",padx=10, pady=10)
        
        
    
# Create the entire GUI program
program = PrimaryFrame()


# Start the GUI event loop
program.window.mainloop()

```
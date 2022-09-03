# Login GUI

If you are need of a Login GUI then this is a great one. Use your Primary script to call this one to get the details and then pass them back.  

  

```

# Import the GUI Module
import tkinter as tk

# The Main Items of the Script
class LoginDetails():
    # Define the Main Window
    def __init__(self):
        self.window = tk.Tk()
        self.window.title("Get Credentials")
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
            
            # Clear the Screen
            entered_value1.delete(0, 'end')
            entered_value2.delete(0, 'end')
            
            # Close the GUI
            self.window.destroy()
        
        
        tk.Label(self.window, text = "Username: ").grid(sticky="W", row = 0, column = 0, padx=10)
        entered_value1 = tk.Entry(self.window, width=40)
        entered_value1.grid(row = 0, column = 1, padx=10, pady=10, sticky="W")
                
        tk.Label(self.window, text = "Password: ").grid(sticky="W", row = 1, column = 0, padx=10, pady=10)
        entered_value2 = tk.Entry(self.window, width=40, show="*")
        entered_value2.grid(row = 1, column = 1, padx=10, pady=10, sticky="W")
        
        # Buttons to Take Action
        quit_button = tk.Button(self.window, text="Cancel", command=self.window.destroy, padx=12)
        quit_button.grid(row=5, column=0,sticky="W",padx=10, pady=10)
        submit_button = tk.Button(self.window, text="Submit",command=actions,padx=5)
        submit_button.grid(row=5, column=1,sticky="E",padx=10, pady=10)
        
    
# Create the entire GUI program
program = LoginDetails()
# Start the GUI event loop
program.window.mainloop()

```
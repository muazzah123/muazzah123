import tkinter as tk
from tkinter import messagebox
from tabulate import tabulate
import numpy as np
from scipy.integrate import simpson
from math import *

# Function to define the integrand
def f(x):
    return eval(expr, {"x": x, "exp": exp, "log": log, "sin": sin, "cos": cos, "tan": tan, "__builtins__": {}})

# Function to display the table using tabulate
def display_table(x_values, y_values):
    data = {"x": x_values, "y": y_values}
    table = tabulate(data, headers="keys", tablefmt="grid")
    table_window = tk.Toplevel(root)
    table_window.title("x and y Values Table")
    text = tk.Text(table_window, wrap='none')
    text.insert(tk.END, table)
    text.pack(expand=1, fill='both')

def calculate_integral():
    try:
        global expr
        expr = expr_entry.get()

        lower_limit = float(lower_limit_entry.get())
        upper_limit = float(upper_limit_entry.get())
        rule = rule_var.get()
        choice = choice_var.get()

        if choice == "p":
            user_input = int(pairs_entry.get())
            if user_input <= 0:
                messagebox.showerror("Error", "Number of pairs must be a positive integer.")
                return
            if rule == "1/3" and user_input % 2 != 0:
                messagebox.showerror("Error", "Invalid number of pairs for Simpson's 1/3 rule. Please enter an even number.")
                return
            if rule == "3/8" and user_input % 3 != 0:
                messagebox.showerror("Error", "Invalid number of pairs for Simpson's 3/8 rule. Please enter a number divisible by 3.")
                return
            h = (upper_limit - lower_limit) / user_input
        elif choice == "h":
            h = float(step_value_entry.get())
            if h <= 0:
                messagebox.showerror("Error", "Step value must be positive.")
                return
            user_input = int((upper_limit - lower_limit) / h)
            if rule == "1/3" and user_input % 2 != 0:
                messagebox.showerror("Error", "Invalid step value. Please enter a value that results in an even number of pairs for Simpson's 1/3 rule.")
                return
            if rule == "3/8" and user_input % 3 != 0:
                messagebox.showerror("Error", "Invalid step value. Please enter a value that results in a number of pairs divisible by 3 for Simpson's 3/8 rule.")
                return

        x_values = np.linspace(lower_limit, upper_limit, user_input + 1)
        y_values = np.vectorize(f)(x_values)

        display_table(x_values, y_values)

        if rule == "1/3":
            result = simpson(y_values, x=x_values, even='first')
        elif rule == "3/8":
            result = simpson(y_values, x=x_values, even='last')

        result_label.config(text=f"The value of the integral using Simpson's {rule} rule is: {result}")

    except Exception as e:
        messagebox.showerror("Error", str(e))

# Create the main window
root = tk.Tk()
root.title("Simpson's Rule Integration")
root.geometry("500x600")
root.configure(bg="#f0f0f0")

# Expression input
tk.Label(root, text="Enter the expression for f(x):", bg="#f0f0f0").pack(pady=5)
expr_entry = tk.Entry(root, width=50)
expr_entry.pack(pady=5)

# Lower limit input
tk.Label(root, text="Enter lower limit of integration:", bg="#f0f0f0").pack(pady=5)
lower_limit_entry = tk.Entry(root, width=50)
lower_limit_entry.pack(pady=5)

# Upper limit input
tk.Label(root, text="Enter upper limit of integration:", bg="#f0f0f0").pack(pady=5)
upper_limit_entry = tk.Entry(root, width=50)
upper_limit_entry.pack(pady=5)

# Simpson's rule choice
tk.Label(root, text="Which Simpson's rule do you want to use? (1/3 or 3/8):", bg="#f0f0f0").pack(pady=5)
rule_var = tk.StringVar(value="1/3")
tk.Radiobutton(root, text="1/3", variable=rule_var, value="1/3", bg="#f0f0f0").pack()
tk.Radiobutton(root, text="3/8", variable=rule_var, value="3/8", bg="#f0f0f0").pack()

# Choice of input method
tk.Label(root, text="Do you want to input the number of pairs or the step value h? (p/h):", bg="#f0f0f0").pack(pady=5)
choice_var = tk.StringVar(value="p")
tk.Radiobutton(root, text="Number of pairs (p)", variable=choice_var, value="p", bg="#f0f0f0").pack()
tk.Radiobutton(root, text="Step value (h)", variable=choice_var, value="h", bg="#f0f0f0").pack()

# Number of pairs input
tk.Label(root, text="Enter number of pairs:", bg="#f0f0f0").pack(pady=5)
pairs_entry = tk.Entry(root, width=50)
pairs_entry.pack(pady=5)

# Step value input
tk.Label(root, text="Enter the step value h:", bg="#f0f0f0").pack(pady=5)
step_value_entry = tk.Entry(root, width=50)
step_value_entry.pack(pady=5)

# Calculate button
calculate_button = tk.Button(root, text="Calculate Integral", command=calculate_integral, bg="#4CAF50", fg="white", font=("Arial", 14))
calculate_button.pack(pady=20)

# Result label
result_label = tk.Label(root, text="", bg="#f0f0f0", font=("Arial", 12))
result_label.pack(pady=10)

# Run the main loop
root.mainloop()


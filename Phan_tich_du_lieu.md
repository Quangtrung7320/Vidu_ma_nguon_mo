import tkinter as tk
from tkinter import ttk, filedialog
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

# Function to find max, min, and mean for each column
def find_stats(dataframe):
    max_values = dataframe.max()
    min_values = dataframe.min()
    mean_values = dataframe.mean()
    return max_values, min_values, mean_values

# Function to display max, min, and mean values for the selected attribute
def display_results(attribute):
    file_path = file_path_entry.get()
    try:
        df = pd.read_csv(file_path)
        df_first_10 = df.head(10)  # Select the first 10 rows

        max_values, min_values, mean_values = find_stats(df_first_10)

        result_text.config(state=tk.NORMAL)
        result_text.delete(1.0, tk.END)
        result_text.insert(tk.END, "Maximum Values (First 10 Rows):\n{}\n\nMinimum Values (First 10 Rows):\n{}\n\nMean Values (First 10 Rows):\n{}".format(max_values, min_values, mean_values))
        result_text.config(state=tk.DISABLED)

        # Plotting the selected attribute for the first 10 rows
        plot_attribute(df_first_10, attribute)

    except FileNotFoundError:
        result_text.config(state=tk.NORMAL)
        result_text.delete(1.0, tk.END)
        result_text.insert(tk.END, "File not found. Please enter a valid file path.")
        result_text.config(state=tk.DISABLED)

# Function to open a file dialog and update the entry widget
def browse_file():
    file_path = filedialog.askopenfilename(filetypes=[("CSV Files", "*.csv")])
    file_path_entry.delete(0, tk.END)
    file_path_entry.insert(0, file_path)

# Function to plot the selected attribute
def plot_attribute(dataframe, attribute):
    ax.clear()  # Clear the previous plot
    ax.bar(dataframe.index, dataframe[attribute])
    ax.set_xlabel('Index')
    ax.set_ylabel(attribute)
    ax.set_title(f'{attribute} Analysis (First 10 Rows)')
    canvas.draw()  # Redraw the canvas

# Function to update the statistics and plot when the attribute changes
def update_on_attribute_change(*args):
    selected_attribute = attribute_var.get()
    display_results(selected_attribute)

# Create the main window
root = tk.Tk()
root.title("Statistical Analysis and Visualization")

# Create an entry for file path
file_path_label = ttk.Label(root, text="Enter CSV File Path:")
file_path_label.pack()
file_path_entry = ttk.Entry(root, width=30)
file_path_entry.pack(side=tk.LEFT, padx=5)
browse_button = ttk.Button(root, text="Browse", command=browse_file)
browse_button.pack(side=tk.LEFT)

# Create a dropdown menu for attribute selection
attribute_label = ttk.Label(root, text="Chọn các thuộc tính:")
attribute_label.pack()
attributes = ["Hours Studied", "Previous Scores", "Extracurricular Activities", "Sleep Hours",
              "Sample Question Papers Practiced", "Performance Index"]
attribute_var = tk.StringVar(root)
attribute_var.set(attributes[0])  # Set the default attribute
attribute_var.trace_add("write", update_on_attribute_change)
attribute_menu = ttk.Combobox(root, textvariable=attribute_var, values=attributes)
attribute_menu.pack()

# Create a button to trigger the calculation and visualization
calculate_button = ttk.Button(root, text="Calculate and Visualize", command=lambda: display_results(attribute_var.get()))
calculate_button.pack(pady=10)

# Create a text widget to display the results
result_text = tk.Text(root, height=10, width=50, state=tk.DISABLED)
result_text.pack()

# Create a figure and axis for plotting
fig, ax = plt.subplots()
canvas = FigureCanvasTkAgg(fig, master=root)
canvas_widget = canvas.get_tk_widget()
canvas_widget.pack()

# Start the GUI main loop
root.mainloop()

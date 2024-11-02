# todolist
import tkinter as tk
from tkinter import messagebox, ttk
import json

# Function to add a task with priority
def add_task():
    task = task_entry.get()
    priority = priority_var.get()
    if task:
        task_data = f"{task} ({priority})"
        task_listbox.insert(tk.END, task_data)
        task_entry.delete(0, tk.END)
    else:
        messagebox.showwarning("Warning", "Please enter a task.")

# Function to remove a selected task
def remove_task():
    selected_task_index = task_listbox.curselection()
    if selected_task_index:
        task_listbox.delete(selected_task_index)
    else:
        messagebox.showwarning("Warning", "Please select a task to remove.")

# Function to clear all tasks
def clear_tasks():
    task_listbox.delete(0, tk.END)

# Function to save tasks to a file
def save_tasks():
    tasks = task_listbox.get(0, tk.END)
    with open("tasks.json", "w") as file:
        json.dump(tasks, file)
    messagebox.showinfo("Info", "Tasks saved successfully.")

# Function to load tasks from a file
def load_tasks():
    try:
        with open("tasks.json", "r") as file:
            tasks = json.load(file)
            for task in tasks:
                task_listbox.insert(tk.END, task)
        messagebox.showinfo("Info", "Tasks loaded successfully.")
    except FileNotFoundError:
        messagebox.showwarning("Warning", "No saved tasks found.")

# Function to mark a task as completed
def toggle_complete_task():
    selected_task_index = task_listbox.curselection()
    if selected_task_index:
        task = task_listbox.get(selected_task_index)
        if task.startswith("[✓] "):
            task = task[4:]  # Remove the completed prefix
        else:
            task = "[✓] " + task  # Add completed prefix
        task_listbox.delete(selected_task_index)
        task_listbox.insert(selected_task_index, task)
    else:
        messagebox.showwarning("Warning", "Please select a task to mark complete.")

# Setting up the main window
app = tk.Tk()
app.title("To-Do List")
app.geometry("400x550")
app.configure(bg="#F5F5F5")

# Header label
header_label = tk.Label(app, text="To-Do List", font=("Helvetica", 16, "bold"), fg="#4a4a4a", bg="#F5F5F5")
header_label.pack(pady=20)

# Input frame for adding tasks
input_frame = tk.Frame(app, bg="#F5F5F5")
input_frame.pack(pady=10)

task_entry = ttk.Entry(input_frame, width=25, font=("Arial", 12))
task_entry.pack(side=tk.LEFT, padx=(0, 10))

priority_var = tk.StringVar(value="Medium")
priority_dropdown = ttk.Combobox(input_frame, textvariable=priority_var, values=["High", "Medium", "Low"], width=10)
priority_dropdown.pack(side=tk.LEFT)

add_task_button = ttk.Button(input_frame, text="Add Task", command=add_task)
add_task_button.pack(side=tk.LEFT, padx=5)

# Styled Listbox to display tasks with scrollbar
task_listbox_frame = tk.Frame(app)
task_listbox_frame.pack(pady=20)

task_listbox = tk.Listbox(
    task_listbox_frame,
    width=35,
    height=12,
    font=("Arial", 12),
    selectbackground="#ADD8E6",
    bg="#FFFFFF",
    bd=0,
    highlightthickness=0,
    activestyle="none"
)
task_listbox.pack(side=tk.LEFT, fill=tk.BOTH)

scrollbar = ttk.Scrollbar(task_listbox_frame, orient="vertical", command=task_listbox.yview)
scrollbar.pack(side=tk.RIGHT, fill=tk.Y)
task_listbox.config(yscrollcommand=scrollbar.set)

# Button frame for task actions
button_frame = tk.Frame(app, bg="#F5F5F5")
button_frame.pack(pady=10)

remove_task_button = ttk.Button(button_frame, text="Remove Task", command=remove_task)
remove_task_button.grid(row=0, column=0, padx=5, pady=5)

clear_tasks_button = ttk.Button(button_frame, text="Clear All", command=clear_tasks)
clear_tasks_button.grid(row=0, column=1, padx=5, pady=5)

save_tasks_button = ttk.Button(button_frame, text="Save Tasks", command=save_tasks)
save_tasks_button.grid(row=1, column=0, padx=5, pady=5)

load_tasks_button = ttk.Button(button_frame, text="Load Tasks", command=load_tasks)
load_tasks_button.grid(row=1, column=1, padx=5, pady=5)

complete_task_button = ttk.Button(button_frame, text="Toggle Complete", command=toggle_complete_task)
complete_task_button.grid(row=2, column=0, columnspan=2, pady=5)

# Style configuration for ttk widgets
style = ttk.Style()
style.configure("TButton", font=("Helvetica", 10), padding=6)
style.configure("TEntry", padding=6)

# Run the application
app.mainloop()

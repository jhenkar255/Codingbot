import tkinter as tk
from tkinter import messagebox
from PIL import Image, ImageTk
import json
import os

FILE_NAME = "tasks.json"

def load_tasks():
    """Load tasks from JSON file"""
    if os.path.exists(FILE_NAME):
        with open(FILE_NAME, "r") as f:
            return json.load(f)
    return []

def save_tasks(tasks):
    """Save tasks to JSON file"""
    with open(FILE_NAME, "w") as f:
        json.dump(tasks, f)


def add_task():
    task = entry.get().strip()
    if task:
        listbox.insert(tk.END, task)
        entry.delete(0, tk.END)
        save_current()
    else:
        messagebox.showwarning("Input Error", "Please enter a task!")

def delete_task():
    try:
        index = listbox.curselection()[0]
        listbox.delete(index)
        save_current()
    except IndexError:
        messagebox.showwarning("Selection Error", "Please select a task to delete!")

def mark_done():
    try:
        index = listbox.curselection()[0]
        task = listbox.get(index)
        if not task.startswith("✔ "):
            listbox.delete(index)
            listbox.insert(tk.END, "✔ " + task)
            save_current()
    except IndexError:
        messagebox.showwarning("Selection Error", "Please select a task to mark as done!")

def save_current():
    """Save current tasks"""
    tasks = list(listbox.get(0, tk.END))
    save_tasks(tasks)

def load_to_list():
    """Load saved tasks into listbox"""
    for t in load_tasks():
        listbox.insert(tk.END, t)

# ---------- GUI Setup ----------
root = tk.Tk()
root.title("To-Do List Application")
root.geometry("500x550")

try:
    bg_image = Image.open("/Users/apple/Desktop/To-Do list App/background.jpg")  # Make sure this file exists
    bg_image = bg_image.resize((500, 550))
    bg_photo = ImageTk.PhotoImage(bg_image)

    bg_label = tk.Label(root, image=bg_photo)
    bg_label.place(x=0, y=0, relwidth=1, relheight=1)
except Exception as e:
    print("Background image not found or invalid. Running with plain background.")
    root.config(bg="#f0f4f7")

# ---------- Frame for Content ----------
frame = tk.Frame(root, bg='#ADD8E6', bd=3, relief="groove")
frame.place(relx=0.5, rely=0.5, anchor="center", width=420, height=450)

# ---------- Heading ----------
tk.Label(
    frame,
    text="📝 To-Do List",
    font=("Helvetica", 18, "bold")
).pack(pady=10)

# ---------- Entry Field ----------
entry = tk.Entry(frame, width=35, font=("Helvetica", 12))
entry.pack(pady=10)

# ---------- Buttons ----------
btn_frame = tk.Frame(frame, bg="#ADD8E6")
btn_frame.pack(pady=5)

tk.Button(
    btn_frame, text="Add Task", width=10, bg="#ADD8E6", fg="black", command=add_task
).grid(row=0, column=0, padx=5)

tk.Button(
    btn_frame, text="Delete Task", width=10, bg="#ADD8E6", fg="black", command=delete_task
).grid(row=0, column=1, padx=5)

tk.Button(
    btn_frame, text="Mark Done", width=10, bg="#ADD8E6", fg="black", command=mark_done
).grid(row=0, column=2, padx=5)

# ---------- Listbox ----------
listbox = tk.Listbox(
    frame,
    width=45,
    height=18,
    font=("Helvetica", 12),
    selectbackground="#a1c4fd"
)
listbox.pack(pady=10)

# ---------- Load saved tasks ----------
load_to_list()

# ---------- Run ----------
root.mainloop()


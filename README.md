# python-Tkinter-proj
import tkinter as tk
from tkinter import ttk
from tkinter import messagebox
import sqlite3

def add_expense():
    try:
        amount = float(amount_entry.get())
        category = category_combo.get()
        description = description_entry.get()
        date = date_entry.get()

        conn = sqlite3.connect('expenses.db')
        c = conn.cursor()
        c.execute('''CREATE TABLE IF NOT EXISTS expenses
                     (id INTEGER PRIMARY KEY AUTOINCREMENT, amount REAL, category TEXT, description TEXT, date TEXT)''')
        c.execute("INSERT INTO expenses (amount, category, description, date) VALUES (?, ?, ?, ?)",
                  (amount, category, description, date))
        conn.commit()
        conn.close()
        update_expense_list()
        messagebox.showinfo("Success", "Expense added successfully")
    except Exception as e:
        messagebox.showerror("Error", f"Failed to add expense: {str(e)}")

def delete_expense():
    try:
        selected_item = expenses_tree.selection()
        if selected_item:
            expense_id = expenses_tree.item(selected_item)['values'][0]
            conn = sqlite3.connect('expenses.db')
            c = conn.cursor()
            c.execute("DELETE FROM expenses WHERE id=?", (expense_id,))
            conn.commit()
            conn.close()
            update_expense_list()
    except Exception as e:
        messagebox.showerror("Error", f"Failed to delete expense: {str(e)}")

def update_expense_list():
    try:
        expenses_tree.delete(*expenses_tree.get_children())
        conn = sqlite3.connect('expenses.db')
        c = conn.cursor()
        c.execute("SELECT * FROM expenses")
        expenses = c.fetchall()
        for expense in expenses:
            expenses_tree.insert('', 'end', values=expense)
        conn.close()
    except Exception as e:
        messagebox.showerror("Error", f"Failed to update expense list: {str(e)}")

root = tk.Tk()
root.title("Expense Tracker")

# Set background color for the root window
root.configure(bg='#F0F0F0')

# Input fields
amount_label = tk.Label(root, text="Amount:", bg='#F0F0F0')
amount_label.grid(row=0, column=0, padx=5, pady=5)
amount_entry = tk.Entry(root, bg='#FFFFFF')
amount_entry.grid(row=0, column=1, padx=5, pady=5)

category_label = tk.Label(root, text="Category:", bg='#F0F0F0')
category_label.grid(row=1, column=0, padx=5, pady=5)
category_combo = ttk.Combobox(root, values=["Food", "Transportation", "Entertainment", "Shopping", "Other"], state='readonly')
category_combo.grid(row=1, column=1, padx=5, pady=5)
category_combo['background'] = '#FFFFFF'

description_label = tk.Label(root, text="Description:", bg='#F0F0F0')
description_label.grid(row=2, column=0, padx=5, pady=5)
description_entry = tk.Entry(root, bg='#FFFFFF')
description_entry.grid(row=2, column=1, padx=5, pady=5)

date_label = tk.Label(root, text="Date (DD-MM-YYYY):", bg='#F0F0F0')
date_label.grid(row=3, column=0, padx=5, pady=5)
date_entry = tk.Entry(root, bg='#FFFFFF')
date_entry.grid(row=3, column=1, padx=5, pady=5)

# Buttons
add_button = tk.Button(root, text="Add Expense", command=add_expense, bg='#4CAF50', fg='#FFFFFF')
add_button.grid(row=4, column=0, columnspan=2, padx=5, pady=5)

delete_button = tk.Button(root, text="Delete Expense", command=delete_expense, bg='#FF5733', fg='#FFFFFF')
delete_button.grid(row=5, column=0, columnspan=2, padx=5, pady=5)

# Set background color for the Treeview widget
style = ttk.Style()
style.configure("Treeview", background="#FFFFFF", foreground="#000000")

# Treeview to display expenses
expenses_tree = ttk.Treeview(root, columns=("Amount", "Category", "Description", "Date"), show='headings')
expenses_tree.heading('Amount', text="Amount")
expenses_tree.heading('Category', text="Category")
expenses_tree.heading('Description', text="Description")
expenses_tree.heading('Date', text="Date")
expenses_tree.grid(row=6, column=0, columnspan=2, padx=5, pady=5)

update_expense_list()

root.geometry('680x510+0+0')
root.mainloop()


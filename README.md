# GAUGING-CUSTOMER-SENTIMENT
This project analyzes student placement feedback using sentiment analysis. Built with Python and Tkinter, it detects if feedback is positive, negative, or neutral using TextBlob. The results are shown instantly and saved to a CSV file, helping improve placement processes and understand student opinions better.

import tkinter as tk
from tkinter import messagebox
from textblob import TextBlob
import pandas as pd
import datetime
import os

# Function to analyze sentiment
def analyze_sentiment(feedback):
    analysis = TextBlob(feedback)
    polarity = analysis.sentiment.polarity
    if polarity > 0:
        return "Positive 😊"
    elif polarity < 0:
        return "Negative 😠"
    else:
        return "Neutral 😐"

# Function to save feedback to CSV
def save_to_csv(name, company, feedback, sentiment):
    date = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    new_data = {
        "Date": [date],
        "Name": [name],
        "Company": [company],
        "Feedback": [feedback],
        "Sentiment": [sentiment]
    }

    df = pd.DataFrame(new_data)

    file_exists = os.path.isfile("placement_feedback_gui.csv")
    df.to_csv("placement_feedback_gui.csv", mode='a', header=not file_exists, index=False)

# Action on submit button click
def submit_feedback():
    name = name_entry.get().strip()
    company = company_entry.get().strip()
    feedback = feedback_text.get("1.0", tk.END).strip()

    if not name or not company or not feedback:
        messagebox.showwarning("Missing Info", "Please fill all fields before submitting.")
        return

    sentiment = analyze_sentiment(feedback)
    result_label.config(text=f"Sentiment: {sentiment}", fg="#1a237e")

    save_to_csv(name, company, feedback, sentiment)
    messagebox.showinfo("Thank You!", "Your feedback has been submitted successfully.")

    # Clear inputs
    name_entry.delete(0, tk.END)
    company_entry.delete(0, tk.END)
    feedback_text.delete("1.0", tk.END)

# --- GUI SETUP ---
app = tk.Tk()
app.title("🎓 Placement Experience Sentiment Analyzer")
app.geometry("650x550")
app.resizable(False, False)
app.config(bg="#f0f4f7")

# Title
tk.Label(app, text="Placement Experience Analyzer", font=("Helvetica", 20, "bold"), bg="#f0f4f7", fg="#1a237e").pack(pady=20)

# Student Name
tk.Label(app, text="👤 Student Name:", font=("Arial", 12), bg="#f0f4f7").pack()
name_entry = tk.Entry(app, width=50, font=("Arial", 12))
name_entry.pack(pady=5)

# Company Name
tk.Label(app, text="🏢 Company Placed In:", font=("Arial", 12), bg="#f0f4f7").pack()
company_entry = tk.Entry(app, width=50, font=("Arial", 12))
company_entry.pack(pady=5)

# Feedback Entry
tk.Label(app, text="📝 Your Placement Experience:", font=("Arial", 12), bg="#f0f4f7").pack()
feedback_text = tk.Text(app, height=6, width=60, font=("Arial", 12), wrap=tk.WORD)
feedback_text.pack(pady=10)

# Submit Button
submit_btn = tk.Button(app, text="Analyze & Submit", font=("Arial", 12, "bold"),
                       bg="#3949ab", fg="white", padx=10, pady=5, command=submit_feedback)
submit_btn.pack(pady=10)

# Sentiment Result Label
result_label = tk.Label(app, text="", font=("Arial", 14, "bold"), bg="#f0f4f7", fg="green")
result_label.pack(pady=10)

# Footer
tk.Label(app, text="© Developed by Nesika V ✨", font=("Arial", 10), bg="#f0f4f7", fg="gray").pack(side="bottom", pady=10)

app.mainloop()

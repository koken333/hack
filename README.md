# hack

import re, threading, time
import tkinter as tk
from tkinter import ttk
from pynput.keyboard import Controller


class MoneyDetectorApp:
    def __init__(self, root):
        self.root = root
        root.title("Money Detector")
        self.running = False
        self.keyboard = Controller()
        self.regex = None
        ttk.Label(root, text="Target amount (USD):").pack(pady=5)
        self.entry = ttk.Entry(root)
        self.entry.insert(0, "50.00")
        self.entry.pack(pady=5)
        self.btn = ttk.Button(root, text="Start", command=self.toggle)
        self.btn.pack(pady=10)
        self.status = ttk.Label(root, text="Status: Stopped")
        self.status.pack(pady=5)
    def toggle(self):
        if not self.running:
            amt = self.entry.get().strip()
            try:
                float(amt) 
            except ValueError:
                self.status.config(text="Invalid amount")
                return
            self.regex = re.compile(rf"\${re.escape(amt)}")
            self.running = True
            self.btn.config(text="Stop")
            self.status.config(text="Status: Running")
            threading.Thread(target=self.detach_listener, daemon=True).start()
        else:
            self.running = False
            self.btn.config(text="Start")
            self.status.config(text="Status: Stopped")
    def detach_listener(self):
        while self.running:
            text = input(">> ")
            if self.regex.search(text):
                self.keyboard.press('e')
                self.keyboard.release('e')
                print("Pressed 'e' for match!")

def main():
    root = tk.Tk()
    app = MoneyDetectorApp(root)
    root.mainloop()

if __name__ == "__main__":
    main()

import time
import datetime
import pygame
import tkinter as tk
from tkinter import messagebox
from random import choice
 
# Initialize Pygame
pygame.init()
 
class AlarmClock:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("Wake Up😴⏰!")
        self.root.configure(bg="#2b2b2b")
 
        # Create input field for alarm time
        self.alarm_time_label = tk.Label(self.root, text="Set Your Alarm", font=("Arial", 24), bg="#2b2b2b", fg="#ffffff")
        self.alarm_time_label.pack(pady=20)
        self.alarm_time_entry = tk.Entry(self.root, font=("Arial", 24), width=10)
        self.alarm_time_entry.pack()
 
        # Create start and stop buttons
        self.start_button = tk.Button(self.root, text="Start", command=self.start_alarm, font=("Arial", 18), bg="#4CAF50", fg="#ffffff")
        self.start_button.pack(pady=10)
        self.stop_button = tk.Button(self.root, text="Stop", command=self.stop_alarm, font=("Arial", 18), bg="#e74c3c", fg="#ffffff", state=tk.DISABLED)
        self.stop_button.pack()
 
        # Create label to display current time
        self.current_time_label = tk.Label(self.root, text="Current Time", font=("Arial", 24), bg="#2b2b2b", fg="#ffffff")
        self.current_time_label.pack(pady=20)
        self.current_time_display = tk.Label(self.root, text="", font=("Arial", 48), bg="#2b2b2b", fg="#ffffff")
        self.current_time_display.pack()
 
        # Create label to display alarm status
        self.alarm_status_label = tk.Label(self.root, text="Alarm Status", font=("Arial", 24), bg="#2b2b2b", fg="#ffffff")
        self.alarm_status_label.pack(pady=20)
        self.alarm_status_display = tk.Label(self.root, text="", font=("Arial", 24), bg="#2b2b2b", fg="#ffffff")
        self.alarm_status_display.pack()
 
        self.is_running = False
        self.alarm_time = None
        self.alarm_sounded = False
 
        self.update_time()
 
    def convert_to_24hr(self, time_str):
        try:
            return datetime.datetime.strptime(time_str, "%I:%M:%S %p").strftime("%H:%M:%S")
        except ValueError:
            messagebox.showerror("Invalid Time Format", "Please use HH:MM:SS AM/PM.")
            return None
 
    def start_alarm(self):
        self.alarm_time = self.convert_to_24hr(self.alarm_time_entry.get())
        if self.alarm_time:
            self.is_running = True
            self.start_button.config(state=tk.DISABLED)
            self.stop_button.config(state=tk.NORMAL)
            self.alarm_status_display.config(text="Alarm is running...")
            self.alarm_sounded = False
 
    def stop_alarm(self):
        self.is_running = False
        self.start_button.config(state=tk.NORMAL)
        self.stop_button.config(state=tk.DISABLED)
        self.alarm_status_display.config(text="Alarm is stopped.")
        pygame.mixer.music.stop()
 
    def update_time(self):
        current_time = datetime.datetime.now().strftime("%H:%M:%S")
        self.current_time_display.config(text=current_time)
 
        if self.is_running and self.alarm_time:
            if abs(datetime.datetime.strptime(current_time, "%H:%M:%S") - datetime.datetime.strptime(self.alarm_time, "%H:%M:%S")).total_seconds() < 1:
                if not self.alarm_sounded:
                    self.alarm_status_display.config(text="WAKE-UP!")
                    alarm_sounds = ["Wooden Train Whistle.mp3", "Rooster.mp3", "Birds Chirping.mp3"]
                    pygame.mixer.music.load(choice(alarm_sounds))
                    pygame.mixer.music.play()
                    self.alarm_sounded = True
 
        self.root.after(1000, self.update_time)
 
    def run(self):
        self.root.mainloop()
 
if __name__ == "__main__":
    alarm_clock = AlarmClock()
    alarm_clock.run()

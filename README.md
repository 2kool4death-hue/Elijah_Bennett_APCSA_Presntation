# Elijah_Bennett_APCSA_Presntation
APCSA Final 
import tkinter as tk
from tkinter import messagebox, ttk

class StressTrackerApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Task Manager & Stress Tracker")
        self.root.geometry("450x550")
        self.root.configure(bg="#f4f6f9")

        # Core State Data Structuring
        # Tasks hold weight/priority representing cognitive load
        self.tasks = [
            {"text": "APCSA Coding Project", "priority": "High", "weight": 40, "completed": False},
            {"text": "Study for Math Exam", "priority": "High", "weight": 35, "completed": False},
            {"text": "Wash the Dishes", "priority": "Low", "weight": 10, "completed": False},
            {"text": "Laundry & Fold", "priority": "Low", "weight": 15, "completed": False}
        ]

        self.create_widgets()
        self.update_stress_meter()

    def create_widgets(self):
        # 1. Header Frame
        header_frame = tk.Frame(self.root, bg="#2c3e50", padding=10)
        header_frame.pack(fill=tk.X)
        
        title_label = tk.Label(
            header_frame, 
            text="Study vs Chores: Stress Tracker", 
            font=("Helvetica", 14, "bold"), 
            fg="white", 
            bg="#2c3e50"
        )
        title_label.pack()

        # 2. Stress Meter UI Component (Dynamic Visual Feedback Loop)
        stress_frame = tk.LabelFrame(self.root, text=" Cognitive Load / Stress Meter ", font=("Helvetica", 10, "bold"), bg="#f4f6f9", padx=15, pady=15)
        stress_frame.pack(fill=tk.X, padx=15, y=15)

        self.stress_label = tk.Label(stress_frame, text="Current Stress: 0%", font=("Helvetica", 11), bg="#f4f6f9")
        self.stress_label.pack(anchor=tk.W)

        # Progress bar representing the dynamic stress tracking
        self.stress_bar = ttk.Progressbar(stress_frame, orient="horizontal", length=350, mode="determinate")
        self.stress_bar.pack(pady=10)

        # 3. Smart Checklist UI Component
        task_frame = tk.LabelFrame(self.root, text=" Tasks & Deliverables ", font=("Helvetica", 10, "bold"), bg="#f4f6f9", padx=15, pady=15)
        task_frame.pack(fill=tk.BOTH, expand=True, padx=15, pady=10)

        self.check_vars = []
        for index, task in enumerate(self.tasks):
            var = tk.BooleanVar(value=task["completed"])
            self.check_vars.append(var)

            # Format the text with weight attributes to show the user how it impacts cognitive load
            display_text = f"[{task['priority']}] {task['text']} (+{task['weight']}% Stress)"
            
            chk = tk.Checkbutton(
                task_frame, 
                text=display_text, 
                variable=var, 
                command=lambda i=index: self.toggle_task(i),
                font=("Helvetica", 10),
                bg="#f4f6f9",
                activebackground="#f4f6f9"
            )
            chk.pack(anchor=tk.W, pady=5)

        # 4. Action Engine Framework (Reset Logic)
        btn_frame = tk.Frame(self.root, bg="#f4f6f9")
        btn_frame.pack(fill=tk.X, padx=15, pady=15)

        reset_btn = tk.Button(
            btn_frame, 
            text="Reset Cycle Engine", 
            command=self.reset_cycle, 
            bg="#e74c3c", 
            fg="white", 
            font=("Helvetica", 10, "bold"),
            relief=tk.FLAT,
            padx=10,
            pady=5
        )
        reset_btn.pack(side=tk.RIGHT)

    def toggle_task(self, index):
        """State persistence and task lifecycle handler."""
        self.tasks[index]["completed"] = self.check_vars[index].get()
        self.update_stress_meter()

    def update_stress_meter(self):
        """Computational logic calculating stress load based on remaining high/low priority deliverables."""
        total_stress = sum(task["weight"] for task in self.tasks if not task["completed"])
        
        # Guard rail to clamp values between 0 and 100
        total_stress = max(0, min(total_stress, 100))
        
        self.stress_bar["value"] = total_stress
        self.stress_label.config(text=f"Current Stress: {total_stress}%")

        # Dynamic aesthetic feedback loop based on computed threshold
        if total_stress > 70:
            self.stress_label.config(fg="#c0392b") # Dark Red for high stress
        elif total_stress > 35:
            self.stress_label.config(fg="#d35400") # Orange for moderate stress
        else:
            self.stress_label.config(fg="#27ae60") # Green for controlled stress

        # Success trigger when all tasks are handled
        if total_stress == 0:
            messagebox.showinfo("Success", "All cognitive loads cleared! Balance achieved.")

    def reset_cycle(self):
        """Prompt-driven structural data refresh mechanism to allow for infinite productivity loops."""
        confirm = messagebox.askyesno("Confirm Reset", "Are you sure you want to restore all items and cycle the simulation?")
        if confirm:
            for index in range(len(self.tasks)):
                self.tasks[index]["completed"] = False
                self.check_vars[index].set(False)
            self.update_stress_meter()


if __name__ == "__main__":
    root = tk.Tk()
    # Apply modern styling configurations
    style = ttk.Style()
    style.theme_use('clam')
    style.configure("TProgressbar", thickness=15, troughcolor="#e0e0e0")
    
    app = StressTrackerApp(root)
    root.mainloop()

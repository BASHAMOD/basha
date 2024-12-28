import tkinter as tk
from tkinter import messagebox, filedialog, ttk
import random
import os
import time
import webbrowser
import pyrebase
from datetime import datetime


# إعداد Firebase
firebase_config = {
    "apiKey": "AlzaSyCU76mpgyBpAjWsTah8jSsvLL2X0DaKt1g",
    "authDomain": "bashman-5d67c.firebaseapp.com",
    "databaseURL": "https://bashman-5d67c-default-rtdb.firebaseio.com/",
    "storageBucket": "bashman-5d67c.appspot.com",
}
firebase = pyrebase.initialize_app(firebase_config)
db = firebase.database()


class BashaModApp:
    def __init__(self, root):
        self.root = root
        self.root.title("MOD BASHA")
        self.root.geometry("800x600")
        self.root.configure(bg="#2C2C2C")

        self.show_login_window()

    def show_login_window(self):
        self.login_window = tk.Frame(self.root, bg="#2C2C2C")
        self.login_window.pack(fill="both", expand=True)

        tk.Label(
            self.login_window,
            text="MOD BASHA",
            font=("Arial", 24, "bold"),
            fg="white",
            bg="#2C2C2C",
        ).pack(pady=20)

        tk.Label(
            self.login_window,
            text="Enter Your Code:",
            font=("Arial", 14),
            fg="white",
            bg="#2C2C2C",
        ).pack(pady=10)

        self.code_entry = tk.Entry(self.login_window, font=("Arial", 14), width=30)
        self.code_entry.pack(pady=10)

        self.enter_button = tk.Button(
            self.login_window,
            text="Enter",
            command=self.verify_code,
            width=20,
            bg="#388E3C",
            fg="white",
            font=("Arial", 12, "bold"),
        )
        self.enter_button.pack(pady=10)

        self.change_button_color()

    def change_button_color(self):
        colors = ["#388E3C", "#F44336", "#1976D2", "#FF5722", "#FF33A1"]
        random_color = random.choice(colors)
        self.enter_button.config(bg=random_color)
        self.root.after(1000, self.change_button_color)

    def verify_code(self):
        user_code = self.code_entry.get().strip()
        if not user_code:
            messagebox.showerror("Error", "Code cannot be empty!")
            return

        # التحقق من الرمز "BASHA MOD VIP"
        if user_code != "BASHA MOD VIP":
            messagebox.showerror("Error", "Invalid code!")
            return

        messagebox.showinfo("Success", "Code verified! Welcome!")
        webbrowser.open("https://t.me/MODBASHA")
        self.root.destroy()  # إغلاق نافذة B1 بالكامل
        self.launch_b2_app()  # تشغيل B2

    def launch_b2_app(self):
        new_root = tk.Tk()
        B2App(new_root)  # تشغيل B2 في نافذة جديدة
        new_root.mainloop()


class B2App:
    def __init__(self, root):
        self.root = root
        self.root.title("BASHA MOD Tool")
        self.root.geometry("800x600")
        self.root.configure(bg="#2C2C2C")

        self.title_label = tk.Label(
            root,
            text="BASHA MOD Tool",
            font=("Arial", 24, "bold"),
            fg="white",
            bg="#2C2C2C",
        )
        self.title_label.pack(pady=10)

        self.change_title_color()

        self.ida_button = tk.Button(
            root,
            text="Select IDA File",
            command=self.select_ida_file,
            width=30,
            bg="#388E3C",
            fg="white",
            font=("Arial", 12, "bold"),
        )
        self.ida_button.pack(pady=10)

        self.lib_button = tk.Button(
            root,
            text="Select Lib File",
            command=self.select_lib_file,
            width=30,
            bg="#F44336",
            fg="white",
            font=("Arial", 12, "bold"),
        )
        self.lib_button.pack(pady=10)

        self.offset_label = tk.Label(
            root,
            text="Number of Offsets:",
            font=("Arial", 14),
            fg="white",
            bg="#2C2C2C",
        )
        self.offset_label.pack(pady=5)

        self.offset_slider = tk.Scale(
            root,
            from_=10,
            to=400,
            orient=tk.HORIZONTAL,
            length=300,
            bg="#2C2C2C",
            fg="white",
            font=("Arial", 12),
        )
        self.offset_slider.pack(pady=10)

        self.progress_bar = ttk.Progressbar(root, orient=tk.HORIZONTAL, length=400, mode='determinate')
        self.progress_bar.pack(pady=10)

        self.generate_button = tk.Button(
            root,
            text="NEW Offsets",
            command=self.generate_code,
            width=30,
            bg="#1976D2",
            fg="white",
            font=("Arial", 12, "bold"),
            state=tk.DISABLED,
        )
        self.generate_button.pack(pady=10)

        self.copy_button = tk.Button(
            root,
            text="Copy Code",
            command=self.copy_code,
            width=30,
            bg="#FF5722",
            fg="white",
            font=("Arial", 12, "bold"),
            state=tk.DISABLED,
        )
        self.copy_button.pack(pady=10)

        self.code_display = tk.Text(
            root,
            height=15,
            width=80,
            font=("Courier", 12),
            bg="#1E1E1E",
            fg="white",
            insertbackground="white",
            state=tk.DISABLED,
        )
        self.code_display.pack(pady=10)

        self.ida_file = ""
        self.lib_file = ""

    def change_title_color(self):
        colors = ["#FF5733", "#33FF57", "#3357FF", "#FF33A1"]
        current_color = self.title_label.cget("fg")
        next_color = random.choice(colors)
        while next_color == current_color:
            next_color = random.choice(colors)
        self.title_label.config(fg=next_color)
        self.root.after(1000, self.change_title_color)

    def select_ida_file(self):
        file_path = filedialog.askopenfilename(
            title="Select IDA File",
            filetypes=(("Executable Files", "*.exe"),),
        )
        if file_path:
            self.ida_file = file_path
            messagebox.showinfo("Success", f"IDA file selected: {self.ida_file}")
            self.check_files_selected()

    def select_lib_file(self):
        file_path = filedialog.askopenfilename(
            title="Select Lib File",
            filetypes=(("Library Files", "*.so"),),
        )
        if file_path:
            allowed_files = {
                "libanogs.so",
                "libanort.so",
                "libCrashSight.so",
                "libUE4.so",
                "libgcloudcore.so",
                "libTDataMaster.so",
                "libgcloudarch.so",
                "libgcloud.so",
            }
            lib_name = os.path.basename(file_path)
            if lib_name in allowed_files:
                self.lib_file = file_path
                messagebox.showinfo("Success", f"Lib file selected: {self.lib_file}")
                self.check_files_selected()
            else:
                messagebox.showerror("Error", f"File '{lib_name}' is not allowed!")

    def check_files_selected(self):
        if self.ida_file and self.lib_file:
            self.generate_button.config(state=tk.NORMAL)

    def generate_code(self):
        offset_count = self.offset_slider.get()

        self.progress_bar["value"] = 0
        self.root.update_idletasks()

        for i in range(101):
            time.sleep(0.03)
            self.progress_bar["value"] = i
            self.root.update_idletasks()

        offsets = set()
        hex_values = set()

        # توليد القيم العشوائية للأوفست
        while len(offsets) < offset_count:
            offsets.add(hex(random.randint(0x10000, 0xFFFFFF)))

        # توليد قيم الهكس العشوائية
        while len(hex_values) < offset_count:
            hex_values.add(" ".join(random.choices("0123456789ABCDEF", k=6)))

        # تحويل المجموعات إلى قوائم
        offsets = list(offsets)
        hex_values = list(hex_values)

        lib_name = os.path.basename(self.lib_file)
        code_lines = []
        for i in range(offset_count):
            code_lines.append(
                f'MemoryPatch::createWithHex("{lib_name}", {offsets[i]}, "{hex_values[i]}");'
            )

        self.code_display.config(state=tk.NORMAL)
        self.code_display.delete(1.0, tk.END)
        self.code_display.insert(tk.END, "\n".join(code_lines))
        self.code_display.config(state=tk.DISABLED)

        self.copy_button.config(state=tk.NORMAL)

    def copy_code(self):
        code = self.code_display.get(1.0, tk.END).strip()
        self.root.clipboard_clear()
        self.root.clipboard_append(code)
        self.root.update()
        messagebox.showinfo("Success", "Code copied to clipboard!")


if __name__ == "__main__":
    root = tk.Tk()
    app = BashaModApp(root)
    root.mainloop()

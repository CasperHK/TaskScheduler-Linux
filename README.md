# ⏰ Linux Task Scheduler (Rust Edition)

A high-performance, lightweight native GUI Task Scheduler for Linux that replicates the core behavior and reliability of the Windows Task Scheduler. 

Built completely in **Rust** using the **`egui`** graphical framework, this tool features zero runtime scripting dependencies and compiles into a single, standalone binary file. Unlike traditional `cron` managers, it leverages **systemd timers** to provide advanced scheduling, task tracking, and automatic recovery of missed tasks.

---

## ✨ Features

* **🏎️ Ultra-Lightweight & Fast**: Compiled native binary with instant startup and minimal RAM footprint.
* **📦 Zero Dependencies**: Users do not need to install Python, Node.js, or heavy UI toolkit packages.
* **🎛️ Windows-Like Reliability**: Utilizes systemd's `Persistent=true` functionality. If the system is turned off or asleep during a scheduled run, the task triggers immediately upon the next boot.
* **🚫 No Crontab Pollution**: Completely independent of the legacy `cron` daemon. It will never break or alter your existing crontab files.
* **🔒 Safe User-Space Operations**: Operates exclusively within `~/.config/systemd/user/`. No `sudo` or root administrative privileges are required.

---

## 🚀 Getting Started

### 📋 Prerequisites
To build this application from source, you only need the standard Rust toolchain installed on your Linux machine.

If you don't have Rust installed, set it up via:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://rustup.rs | sh
```

Depending on your distribution, ensure basic graphic development libraries are available (required by `egui`/`eframe`):
* **Ubuntu / Debian / Mint:** `sudo apt install build-essential libxcb-render0-dev libxcb-shape0-dev libxcb-xfixes0-dev libxkbcommon-dev libssl-dev`
* **Fedora:** `sudo dnf groupinstall "Development Tools" && sudo dnf install libxkbcommon-devel openssl-devel`
* **Arch Linux:** `sudo pacman -S base-devel libxkbcommon`

### 📦 Building and Running
1. Clone or navigate to your project directory:
   ```bash
   cd linux_scheduler
   ```
2. Compile and run the development version:
   ```bash
   cargo run
   ```
3. Generate a highly optimized production standalone binary:
   ```bash
   cargo build --release
   ```
   Your portable, single-file executable will be available at **`target/release/linux_scheduler`**.

---

## 🛠️ How It Works Under the Hood

When you define a task (e.g., `my_rust_task`), the application safely creates two structured system configuration profiles:

1. **`~/.config/systemd/user/my_rust_task.service`** — Specifies *what* command or executable path to trigger.
2. **`~/.config/systemd/user/my_rust_task.timer`** — Specifies *when* and how often to trigger it.

The Rust binary then communicates via native system processes to instantly initialize and start your task cycle:
```bash
systemctl --user daemon-reload
systemctl --user enable my_rust_task.timer
systemctl --user start my_rust_task.timer
```

---

## 🖥️ Managing Tasks via Terminal (CLI Companion)

Since your tasks are registered as standard system units, you can fully inspect and manage them using standard terminal tools:

* **List all active timers created by the app:**
  ```bash
  systemctl --user list-timers
  ```
* **Read live execution and error logs:**
  ```bash
  journalctl --user -u my_rust_task.service
  ```
* **Manually execute a task instantly (Run Now behavior):**
  ```bash
  systemctl --user start my_rust_task.service
  ```
* **Manually tear down a task:**
  ```bash
  systemctl --user disable --now my_rust_task.timer
  rm ~/.config/systemd/user/my_rust_task.*
  systemctl --user daemon-reload
  ```

---

## 📜 License
This project is open-source and available under the MIT License.

# System Monitor (lookin)

A cli tool built with kotlin that helps monitor system stats:

- CPU and Memory
- Disk storage
- Network (ethernet, wifi, bluetooth)
- USB devices
- Audio devices

It will have a main screen like a cockpit for overview and more detailed screens.
Simple navigation commands will be used to navigate screens

## The UI

### 🎛️ Dashboard / Cockpit

╔═════════════════════🌐 UNIVERSAL DEVICE MONITOR 🌐════════════════════╗
║                                                                       ║
║   CPU      [██████░░░░░░░] 45%   Memory [███████░░░░░░] 5.3GB / 16GB  ║
║   Disk     [████████░░░░░] 60%   Network eth0 ↑3MB ↓1MB               ║
║   Wi-Fi: Connected                     USB: 3 Devices                 ║
║   Bluetooth: 2 Devices 🎧            Audio: 2 Devices 🎵             ║
╠═══════════════════════════════════════════════════════════════════════╣
║ Alerts:                                                               ║
║ • USB removed                                                         ║
║ • CPU temp high 75°C                                                  ║
║ • Audio device disconnected                                           ║
╠═══════════════════════════════════════════════════════════════════════╣
║ Navigation: [1] CPU/Memory  [2] Disk    [3] Network                   ║
║             [4] USB/BT      [5] Audio                                 ║
║             [L] Logs  [Q] Quit                                        ║
╚═══════════════════════════════════════════════════════════════════════╝

### 💻 CPU / Memory Detail

╔════════════════💻 CPU & MEMORY 💻════════════╗
║ CPU Cores:                                    ║
║ Core0 [███████░░] 65%  Temp: 62°C             ║
║ Core1 [█████░░░░] 55%  Temp: 60°C             ║
║ Core2 [████░░░░░] 40%  Temp: 58°C             ║
║ Core3 [█████░░░░] 50%  Temp: 59°C             ║
╠════════════════ Memory ═══════════════════════╣
║ RAM  [████████░░] 5.3GB / 16GB                ║
║ Swap [██░░░░░░░] 2GB / 8GB                    ║
╚═══════════════════════════════════════════════╝
[Esc] Back  [R] Refresh

### Disk / Storage Detail

╔════════════════💽 DISK MONITOR 💽══════════════════════╗
║ Device       Mount      FS     Used / Total             ║
║ /dev/sda     /          ext4   [███████░░░] 40GB /100GB ║
║ /dev/sdb     /mnt/usb   vfat   [████░░░░░] 8GB /16GB    ║
╠════════════════ Partitions ═════════════════════════════╣
║ sda1: /boot  [█░░░░░░░] 1GB / 2GB                       ║
║ sda2: /      [███████░░] 39GB / 98GB                    ║
╚═════════════════════════════════════════════════════════╝
[Esc] Back  [R] Refresh

### Network Detail

╔════════════════🌐 NETWORK MONITOR 🌐═════════════════════╗
║ Interface  Status      IP             ↑ / ↓               ║
║ eth0       UP          192.168.1.10  [█░░░░░] 3MB / 1MB   ║
║ wlan0      Connected   192.168.1.20  [██░░░░] 1MB / 0.5MB ║
║ lo         UP          127.0.0.1     [-]                  ║
╠════════════════ Active Connections ═══════════════════════╣
║ 192.168.1.5 : 80   ESTABLISHED                            ║
║ 8.8.8.8 : 53      ESTABLISHED                             ║
╚═══════════════════════════════════════════════════════════╝
[Esc] Back  [R] Refresh

### USB / Bluetooth Devices

╔════════════════🔌 DEVICES 🔌═══════════════╗
║ USB Devices:                               ║
║ 1. Logitech Mouse      /dev/input/mouse0   ║
║ 2. Kingston USB Stick  /dev/sdb1           ║
║                                            ║
║ Bluetooth Devices:                         ║
║ 1. Sony WH-1000XM4    Connected            ║
║ 2. Phone Audio         Paired              ║
╚════════════════════════════════════════════╝
[Esc] Back  [R] Refresh

### 🎵 Audio Devices

╔════════════════🎵 AUDIO DEVICES 🎵════════════╗
║ Speakers / Headphones:                         ║
║ 1. Sony WH-1000XM4   Connected                 ║
║ 2. Bluetooth Speaker  Paired                   ║
║                                                ║
║ Microphones:                                   ║
║ 1. Built-in Mic     Active                     ║
║ 2. USB Mic          Idle                       ║
╠════════════════ Volume Levels ═════════════════╣
║ Master [███████░░] 70%                         ║
║ Mic    [████░░░░] 40%                          ║
║                                                ║
║ Waveform Preview:                              ║
║ ───█────███────████─███────█───────────────────║
╚════════════════════════════════════════════════╝
[Esc] Back  [R] Refresh

### ⚡ Alerts & Logs

╔════════════════⚠ ALERTS & LOGS ⚠═════════════╗
║ [12:32] USB: Kingston Stick removed           ║
║ [12:35] CPU: Temp high (78°C)                 ║
║ [12:36] Network: wlan0 disconnected           ║
║ [12:37] Disk: /dev/sdb read error             ║
║ [12:38] Audio: Microphone disconnected        ║
╚═══════════════════════════════════════════════╝
[Esc] Back  [C] Clear Logs  [R] Refresh

## The Architechture

The tool has performs two main tasks:

1. Executing common linux commands:

- lsub
- netstat
- nmcli
- inotifywait

reading system files:

- /dev
- /proc
- /sys
- /run

priodically, parsing it and exposing them through a Kotlin flow of specific data classes.

2. Collect the information in coroutine flows and presenting it on screen.
← → ↑ ↓ ◜◝ ◞ ◟ ┘┌ │ ─ └ ┐ ▲ ▼ ▶ ◀
╔════════════════════════════════════════════════════════════════════════════════════════════════╗
║                                                                                                ║
║   ┌───────────────────┐         ┌───────────────────┐                                          ║
║   │ Command execution │         │ Parsing output    │                                          ║
║   │ (nmcli, lsub ...) │────────▶│ into data classes │                                          ║  
║   │ Or file read      │         └───────────────────┘                                          ║
║   │ (/dev, /proc ...) │                   │                                                    ║
║   └───────────────────┘                   │                                                    ║  
║          ▲                                │                                                    ║
║          │                                │                                                    ║  
║          │                                │                                                    ║
║          │                                ▼                                                    ║
║          │                     ┌───────────────┐                    ┌───────────────────┐      ║
║          └─────────────────────│ Exposing data │───────────────────▶│ Collect flows and │      ║
║                                │ as flows      │                    │ present on UI     │      ║
║                                └───────────────┘                    └───────────────────┘      ║
║                                                                                                ║
╚════════════════════════════════════════════════════════════════════════════════════════════════╝

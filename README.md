# CLI System Monitor: Docs

Design documentation for
[CLI-System-Monitor](https://github.com/NinjaShadowBoy/CLI-System-Monitor),
a Kotlin command-line tool (working name "lookin") that monitors a Linux
machine from the terminal: CPU and memory, disk storage, network (Ethernet,
Wi-Fi, Bluetooth), USB devices and audio devices.

There is no code here. The repository holds one document,
[Idea-presentation-and-ui-mockups.md](Idea-presentation-and-ui-mockups.md),
which covers:

- **The idea:** a "cockpit" overview screen plus detail screens, switched with
  single keys (`1` to `5`, `L` for logs, `Q` to quit)
- **UI mockups** in box-drawing characters for every screen: dashboard,
  CPU/memory, disk, network, USB/Bluetooth, audio, and alerts and logs
- **The architecture:** collectors run Linux commands (`lsusb`, `netstat`,
  `nmcli`, `inotifywait`) or read `/proc`, `/sys`, `/dev` and `/run`, parse the
  results into data classes, and expose them as Kotlin flows that the UI
  collects and draws

## Read it

Open the document on GitHub, or clone the repository:

```bash
git clone https://github.com/NinjaShadowBoy/CLI-System-Monitor-Docs.git
```

The mockups use box-drawing characters, so view them in a monospaced font.

The booting process in Linux involves several stages, starting from the system's power-on to the loading of the operating system. Understanding this process is crucial for troubleshooting and system management.

Linux Booting Process:

![image](https://github.com/user-attachments/assets/2cf203ff-fd8a-440e-8f1e-d05144c679c3)


# 🐧 Linux Booting Process

The booting process in Linux involves several stages, starting from system power-on to loading the operating system. Understanding this is crucial for troubleshooting and system management.


* **BIOS/UEFI Initialization**

  * When the system is powered on, the **BIOS** (Basic Input/Output System) or **UEFI** (Unified Extensible Firmware Interface) initializes hardware and performs **POST** (Power-On Self Test).

* **Boot Loader**

  * A boot loader like **GRUB (Grand Unified Bootloader)** is loaded.
  * It shows the OS selection menu and loads the **Linux kernel** into memory.

* **Kernel Initialization**

  * The Linux **kernel** is initialized.
  * It sets up system memory, detects hardware, and mounts the **root filesystem**.

* **Init System**

  * The **init system** (e.g., `systemd`) takes control.
  * It reads configuration files and starts system **services and processes**.

* **Runlevel / Target**

  * The system enters a specific **target** (or legacy **runlevel**), defining the machine's operational mode (e.g., GUI, CLI, maintenance).

---

### 🎯 Different Targets in `systemd` (Modern Linux Systems)

* `graphical.target`:
  → Boots with a **Graphical User Interface (GUI)**

* `multi-user.target`:
  → Multi-user mode **without GUI** (text console)

* `rescue.target`:
  → **Single-user mode** for recovery and maintenance

* `emergency.target`:
  → Minimal environment for **critical recovery**

---

### 🔍 View or Change Default Target

* **Check default target:**

  ```bash
  systemctl get-default
  ```

* **Set a new default target:**

  ```bash
  sudo systemctl set-default <target>
  ```

  Replace `<target>` with options like:

  * `graphical.target`
  * `multi-user.target`

---

### ✅ Summary

* The Linux boot process includes **BIOS/UEFI**, **boot loader**, **kernel**, **init system**, and finally entering a **target**.
* Targets define the system’s **operational state**.
* You can use `systemctl` to **view** or **change** the default target.
* Mastery of the boot process is essential for effective Linux **administration and troubleshooting**.


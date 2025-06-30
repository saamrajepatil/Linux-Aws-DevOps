The booting process in Linux involves several stages, starting from the system's power-on to the loading of the operating system. Understanding this process is crucial for troubleshooting and system management.

Linux Booting Process

BIOS/UEFI Initialization: When the system is powered on, the Basic Input/Output System (BIOS) or Unified Extensible Firmware Interface (UEFI) initializes hardware components and performs a Power-On Self Test (POST).

Boot Loader: The boot loader, such as GRUB (Grand Unified Bootloader), is loaded into memory. It presents a menu to select the operating system and loads the kernel into memory.

Kernel Initialization: The Linux kernel is loaded and initialized. It sets up system memory, detects hardware, and mounts the root filesystem.

Init System: The init system, like systemd, takes over from the kernel to manage system processes. It reads configuration files and starts services.

Runlevel/Target: The system enters a specific runlevel or target, which defines the state of the machine (like multi-user mode or graphical mode).

Different Targets in Linux

Linux systems using systemd have different targets that define the state of the system:

graphical.target: Starts the system with a graphical user interface.
multi-user.target: Provides a multi-user environment without a graphical interface.
rescue.target: Boots into a single-user mode for maintenance.
emergency.target: Provides minimal environment for emergency situations.

Viewing and Changing Default Target

To see the default target in a Linux system using systemd, you can use the following command:

systemctl get-default

To change the default target, use:

sudo systemctl set-default <target>

Replace <target> with your desired target, like graphical.target or multi-user.target.

Summary

The Linux booting process involves several stages, from BIOS/UEFI initialization to entering a specific target. Different targets define the operational state of the system, and you can view or change the default target using systemd commands. Understanding these concepts helps in managing and troubleshooting Linux systems effectively.

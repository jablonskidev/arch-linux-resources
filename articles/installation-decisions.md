# Installing Arch Linux: Key Decisions for First-Time Users

**[Note: This article is a work in progress.]**

- [Overview](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#overview)
- [Pre-Installation Planning](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#pre-installation-planning)
  - [Check Your Hardware](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#check-your-hardware)
  - [Create Installation Media](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#create-installation-media)
- [Critical Installation Decisions](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#critical-installation-decisions)
  - [Disk Partitioning Strategy](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#disk-partitioning-strategy)
    - [Single vs Dual Boot](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#single-vs-dual-boot)
    - [Partition Scheme (UEFI Systems)](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#partition-scheme-uefi-systems)
    - [File System Choice](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#network-configuration)
  - [Network Configuration](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#network-configuration)
    - [During Installation](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#during-installation)
    - [After Installation](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#after-installation)    
  - [User Account Setup](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#user-account-setup)
  - [Boot Loader Choice](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#boot-loader-choice)
  - [Desktop Environment Planning](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#desktop-environment-planning)
    - [Display Server](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#display-server)
    - [Desktop Environment Options](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#desktop-environment-options)
    - [Essential First Software](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#essential-first-software)
- [Decision Summary Checklist](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#decision-summary-checklist)
- [Troubleshooting Common Decision Points](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#troubleshooting-common-decision-points)
- [Final Tips](https://github.com/jablonskidev/arch-linux-resources/blob/main/articles/installation-decisions.md#final-tips)

## Overview

When you install Arch Linux, you need to make a lot of decisions that are abstracted away for you with other distros. You can use this short and skimmable article to make more informed decisions during your first install.

You'll get:
- Just enough context to make decisions
- Suggested choices for uncertain beginners
- A decision checklist
- Troubleshooting tips

## Pre-Installation Planning

### Check Your Hardware

| What to Check | How to Check | Why It Matters |
|---------------|--------------|----------------|
| Boot Mode | BIOS settings or `ls /sys/firmware/efi` | Determines partition scheme |
| Disk Space | Available unpartitioned space | Affects partition sizes |
| Network | Ethernet cable or WiFi info | Needed for package downloads |

### Create Installation Media

- Download ISO from archlinux.org
- Verify with checksums
- Use Rufus (Windows) or `dd` (Linux) to create USB

## Critical Installation Decisions

### Disk Partitioning Strategy

#### Single vs Dual Boot

**Why this matters**: Determines how you partition your disk and affects bootloader complexity

| Setup | Pros | Cons | Recommended For |
|-------|------|------|-----------------|
| **Single Boot** | Simple setup, full disk control, no conflicts | No Windows fallback, harder to share files | Dedicated Linux users, old hardware |
| **Dual Boot** | Keep familiar OS, easier transition | Boot menu complexity, Windows updates can break Linux boot | New users, work requirements, gaming |

#### Partition Scheme (UEFI Systems)

**Why partition layout matters**: Affects system performance, upgrade ease, and data safety

| Partition | Size | Purpose | Required? | Why This Size? |
|-----------|------|---------|-----------|----------------|
| **EFI** | 512MB | Boot files | Yes (UEFI) | Enough for multiple kernels + Windows dual boot |
| **Root (/)** | 20-50GB | System files | Yes | Base system ~4GB, leaves room for packages |
| **Swap** | RAM size or 8GB | Virtual memory | Recommended | Prevents crashes when RAM full, enables hibernation |
| **Home (/home)** | Remaining space | User data | Optional but recommended | Keeps personal files safe during system reinstalls |

**Quick Decision**: Use separate /home partition. This protects your data if you need to reinstall the system.

#### File System Choice

**Why filesystem matters**: Affects reliability, performance, and available features

- **ext4:** Proven stable over decades, good performance, easy recovery tools
- **Btrfs:** Built-in snapshots and compression, but more complex and newer
- **Others:** Specialized use cases

**Why ext4 for beginners**:SincemMost documentation assumes ext4, there will be fewer variables when troubleshooting.

### Network Configuration

#### During Installation

- **Wired**: Usually works automatically
- **Wireless**: `iwctl` commands needed
  
```
iwctl station wlan0 scan
iwctl station wlan0 get-networks
iwctl station wlan0 connect "Network Name"
```

#### After Installation

**Why network manager matters**: Determines how you'll connect to internet long-term and manage connections

| Option | Best For | Complexity | Why Choose |
|--------|----------|------------|------------|
| **NetworkManager** | Desktop users, WiFi | Easy | GUI tools, automatic reconnection, handles complex networks |
| **systemd-networkd** | Servers, advanced users | Complex | Lightweight, no GUI needed, more control |

**Quick Decision**: Choose NetworkManager for desktop systems. It handles WiFi passwords, VPNs, and network switching automatically.

### User Account Setup

**Why user accounts matter**: Root access is dangerous for daily use. You need a regular user account with sudo privileges for safety.

Essential steps:
- Set strong root password (emergency access only)
- Create regular user: `useradd -m -G wheel username`
- Enable sudo: uncomment `%wheel` line in `visudo`

**Why to not use root daily**: One wrong command can destroy your system. Sudo asks for confirmation on dangerous operations.

### Boot Loader Choice

**Why bootloader matters**: Without it, your computer can't find and start your operating system.

| Boot Loader | Best For | Pros | Cons | Why Choose |
|-------------|----------|------|------|------------|
| **GRUB** | Dual boot, BIOS systems | Handles Windows dual boot, works everywhere | More complex setup | Universal compatibility, mature |
| **systemd-boot** | Single boot UEFI | Simple config, fast boot | UEFI only, basic features | Minimal and reliable |

**Quick Decision**: Use GRUB for dual booting (handles Windows detection), and use systemd-boot for UEFI single boot (simpler).

## Desktop Environment Planning

### Display Server

**Why this choice matters**: Determines what graphics technology your desktop uses, so it affects performance and compatibility

| Option | Status | Use When | Why Choose |
|--------|--------|----------|------------|
| **X11** | Mature, stable | Older hardware, compatibility needed | Works with everything, extensive software support |
| **Wayland** | Modern, secure | New hardware, modern software only | Better security, smoother graphics, future-focused |

**Quick Decision**: Start with X11—fewer compatibility issues. Switch to Wayland later once you're comfortable.

### Desktop Environment Options

**Why DE choice matters**: This affects how you interact with your computer daily.

Beginner-friendly choices:

| DE | RAM Usage | Customization | Learning Curve | Why Choose |
|----|-----------|---------------|----------------|-------------|
| **GNOME** | High (1GB+) | Limited but polished | Low | Modern design, works out of box, like macOS feel |
| **KDE Plasma** | Medium (512MB) | Extensive | Medium | Highly customizable, Windows-like, feature-rich |
| **XFCE** | Low (256MB) | Moderate | Low | Lightweight, traditional layout, good for old hardware |

**Quick Decision**: GNOME for modern simplicity, KDE for customization and familiarity, XFCE if hardware is limited

### Essential First Software

**Why install**: You'll need them to configure your system and get online help.

Install immediately after DE:
- **Text editor:** `nano` (simple) or `vim` (powerful) for editing config files
- **Browser:** `firefox` to access Arch Wiki and documentation  
- **AUR helper:** `yay` to access to community packages not in official repos
- **File manager (if not included):** To navigate and manage files graphically

## Decision Summary Checklist

### Safe Defaults for Uncertain Users

**Why these defaults**: These choices work reliably for most users and are well-documented.

- ✅ **Partitions**: 512MB EFI + 20GB root + 8GB swap + remaining home *(standard layout, room to grow)*
- ✅ **Filesystem**: ext4 for all partitions *(proven stable, easy to fix)*  
- ✅ **Network**: NetworkManager *(handles WiFi automatically)*
- ✅ **Bootloader**: GRUB *(works with dual boot)*
- ✅ **Display**: X11 *(maximum compatibility)*
- ✅ **Desktop**: GNOME or XFCE *(simple and stable)*

### Pre-Install Decisions
- [ ] Boot mode confirmed (UEFI/BIOS)
- [ ] Partition sizes planned
- [ ] Network connection method ready
- [ ] Username chosen

### During Install Decisions
- [ ] Partition scheme implemented
- [ ] Root password set
- [ ] User account created
- [ ] Bootloader installed

### Post-Install Decisions
- [ ] Desktop environment chosen
- [ ] Essential software installed
- [ ] System updated

## Troubleshooting Common Decision Points

### "I'm Not Sure About My Hardware"

- **Boot mode**: If you have Windows 10/11, you're probably UEFI
- **Disk space**: Use `lsblk` to see current partitions
- **Graphics**: Intel/AMD work out of box, NVIDIA needs extra drivers

### "I Made the Wrong Choice"

**Why some choices are easier to change**: System-level decisions require more work to modify than user-level ones.

Can change later / user-level settings:
- **Desktop environment:** just install another
- **Network manager:** switch and restart
- **Most software choices:** install/uninstall anytime

Hard to change / system-level decisions:
- **Partition scheme:** data on disk, requires backup/restore
- **Bootloader:** controls system startup, risky to change

### Getting Help

- **Arch Wiki:** Your primary resource
- **Forum:** bbs.archlinux.org
- **Reddit:** r/archlinux
- **IRC:** #archlinux on Libera.Chat

### Recovery Options

- **Boot from USB:** Always keep installation media
- **Chroot rescue:** `arch-chroot /mnt` from live environment
- **Reinstall:** Sometimes faster than fixing complex issues

## Final Tips

Before you start:
- Read the official Installation Guide
- Have another device for looking things up
- Budget a few hours for you first install

During installation:
- Take your time with partitioning
- Double-check commands before running
- Don't skip the bootloader step

After installation:
- **Update system immediately:** `sudo pacman -Syu`
- **Install microcode:** `intel-ucode` or `amd-ucode`
- **Set up firewall:** `sudo systemctl enable ufw`

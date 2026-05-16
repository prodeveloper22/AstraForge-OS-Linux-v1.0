# AstraForge Linux  
A custom Ubuntu‑Base 22.04 live distribution built by Berken.  
AstraForge focuses on speed, simplicity, and a clean installer experience powered by Calamares.

---

## Overview
AstraForge is a lightweight Linux distribution created from scratch using:
- Ubuntu Base 22.04 root filesystem  
- Custom chroot environment  
- Calamares graphical installer  
- ISOLINUX bootloader  
- SquashFS compressed root filesystem  
- Custom kernel integration (5.15.x LTS)

The system is designed to boot as a live environment and optionally install to disk.

---

## System Components
### **Kernel**
AstraForge uses the Ubuntu LTS kernel:
- **linux-image-5.15.x-generic**
- **linux-modules-extra-5.15.x-generic**

### **Initrd**
Generated inside the chroot and copied to:
```
/iso/casper/initrd
```

### **Root Filesystem**
Compressed using SquashFS:
```
/iso/casper/filesystem.squashfs
```

### **Bootloader**
ISOLINUX (BIOS boot) with:
- `isolinux.bin`
- `boot.cat`
- `isohdpfx.bin` (MBR)

---

## Build Process Summary
### **1. Prepare chroot (OUTSIDE CHROOT)**
- Extract Ubuntu Base  
- Bind‑mount `/dev`, `/proc`, `/sys`  
- Copy DNS config  

### **2. Configure system (INSIDE CHROOT)**
- Install kernel  
- Install Calamares  
- Configure system files  

### **3. Export filesystem (OUTSIDE CHROOT)**
- Copy kernel + initrd  
- Build SquashFS  
- Build ISO with xorriso  

---

## Build Commands (Short Version)
### **Kernel + Initrd (OUTSIDE CHROOT)**
```
sudo cp /chroot/boot/vmlinuz-5.15* /iso/casper/vmlinuz
sudo cp /chroot/boot/initrd.img-5.15* /iso/casper/initrd
```

### **SquashFS (OUTSIDE CHROOT)**
```
sudo mksquashfs /chroot /iso/casper/filesystem.squashfs -e boot
```

### **ISO Build (OUTSIDE CHROOT)**
```
sudo xorriso -as mkisofs \
  -r -V "AstraForge" \
  -o AstraForge.iso \
  -J -l -cache-inodes \
  -isohybrid-mbr /usr/lib/ISOLINUX/isohdpfx.bin \
  -partition_offset 16 \
  -b isolinux/isolinux.bin \
     -c isolinux/boot.cat \
     -no-emul-boot -boot-load-size 4 -boot-info-table \
  /iso
```

---

## Directory Structure
```
astra-forge/
├── chroot/                 # Full root filesystem
├── iso/
│   ├── casper/
│   │   ├── filesystem.squashfs
│   │   ├── initrd
│   │   └── vmlinuz
│   ├── isolinux/
│   │   ├── isolinux.bin
│   │   ├── boot.cat
│   │   └── isolinux.cfg
│   └── .disk/
│       ├── info
│       └── release_notes_url
└── AstraForge.iso
```

---

## Testing
AstraForge can be tested using:
- GNOME Boxes  
- VirtualBox  
- QEMU  
- Real hardware (USB boot)

---

## Notes
- The host kernel (6.17.x) is **not** used in the ISO.  
- Only the 5.15.x kernel inside chroot matters.  
- dpkg errors about the host kernel inside chroot are safe to ignore.  
- SquashFS build time depends on disk speed and CPU.

---

## Contact
If you have any Questions, Suggestions or anything else, Let me know at -
E-Mail: telliberken@gmail.com  

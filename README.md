# Unified Kernel Image automation with dracut
Config / Automation for Unified Kernel Images (UKIs) directly booted from UEFI menu with dracut and efibootmgr with a normal and an optional fallback UKI per installed kernel and (optionally) with Secure Boot enabled.

**Use at your own risk**  
I use this with my Void Linux install on my laptop. 
These scripts will create _signed_ (for Secure Boot) bootable efi files and boot-entries in the UEFI menu of your motherboard/laptop. No bootloader needed!

The files provided here here are automatically used by `xbps-install` / `xbps-remove` and `vkpurge` if placed in the appropriate directories, see Installation section.

# Installation
1. Clone repo
2. Place files in appropriate directories in `/etc` as root.
3. Configure.

In general there's no need to edit files in `/etc/kernel.d` You can adjust things in:
- `/etc/default/dracut-uki-hook`
- `/etc/default/efibootmgr-uki-hook`
- The files in `/etc/dracut` (kernel command line, plymouth, etcetera).

Enable everything by setting `UKI_ON=1` in `/etc/default/dracut-uki-hook`.

## Dependencies
- `sbsigntool`. For dracut to work with Secure Boot.
- `binutils` (provides `objcopy`). For dracut to build UKI files.
- `gummiboot-efistub`. For dracut to build UKI files.

## Optional dependencies
The default files here assume you have `plymouth` installed and microcode update-files for your kernel: `intel-ucode` if you have an Intel CPU. You can change this in de dracut config files in `/etc/dracut`.

## Notes
- If microcode is updated, you need to manually run  `xbps-reconfigure --force linux<x>.<y>`. I haven´t found a way to do this in an automated way (yet), but tips are welcome.
- The easiest way to setup Secure Boot in te first place, is via `sbctl`  - install it. See [Arch wiki](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot) section on sbctl.
- Anything you put in `/etc/dracut.conf` or in conf files in `/etc/dracut.conf.d` will **always** be parsed by dracut. That is the reason to make 2 seperate configs in `/etc/dracut`: to have the option to have different configs for _normal_ and _fallback_ images that are independent from Void updates.
- These files will install a normal UKI **and** optionally a fallback UKI per kernel, see Installation section. For instance, if you installed `linux` and `linux-lts` kernel packages, you will potentially end up with **4** UKIs (2 normal, 2 fallback).
- If a minor update of a kernel is installed, these scripts will remove the old files and UEFI entries and replace it with the new ones. Only with a new major version (for instance 6.4 becomes 6.5) then new files and entries will be created.
- You still need to clean old kernels from time to time with `vkpurge`. The remove-scripts here are used by vkpurge.
- Before this automation works as intended, you have to manually set your preferred order with `efibootmgt -o x,y,z`. This is also true for every **new** kernel you install.


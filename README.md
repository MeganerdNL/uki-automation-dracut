# uki-automation-dracut
Config / Automation for Unified Kernel Images (UKIs) directly booted from UEFI menu with dracut and efibootmgr with a normal and an optional fallback image per kernel and Secure Boot enabled.

**Use at your own risk**  
I use this with my Void Linux install on my laptop. 
These scripts will create _signed_ (for Secure Boot) bootable efi files and boot-entries in the UEFI menu of your motherboard/laptop. No bootloader needed!

The scripts here are automatically used by `xbps-install` / `xbps-remove` and `vkpurge`.

# Installation
1. Clone repo
2. Place files in appropriate directories as root.
3. Configure.

In general there's no need to edit files in `/etc/kernel.d` You can adjust things in:
- `/etc/default/dracut-uki-hook`
- `/etc/default/efibootmgr-uki-hook`
- The files in `/etc/dracut` (kernel command line, plymouth, etcetera).

Enable everything by setting `UKI_ON` to `1` in `/etc/default/dracut-uki-hook`.

## Dependencies
- `sbsigntool`. For dracut to work with Secure Boot.
- `binutils` (provides `objcopy`). For dracut to build UKI files.
- `gummiboot-efistub`. For dracut to build UKI files

## Optional dependencies
- De default files here assume you have `plymouth` installed and microcode update-files for your kernel: `intel-ucode` if you have an Intel CPU. You can change this in de dracut config files in `/etc/dracut`.

## Some notes
- The easiest way to setup Secure Boot in te first place, is via `sbctl`  - install it. See [Arch wiki](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot) section on sbctl.
- Anything you put in `/etc/dracut.conf` or in conf files in `/etc/dracut.conf.d` will **always** be parsed by dracut. That is the reason to make 2 seperate configs in `/etc/dracut`: to have the option to have different configs for _normal_ and _fallback_ images that are independent from Void updates.
- These files will install a normal bootable efi file **and** optional a fallback bootalble efi file per kernel, see Installation below. If you, for instance  installed `linux` and `linux-lts` kernel packages, you will potentionally end up with **4** bootable efi files (2 normal, 2 fallback).
- If a minor update of a kernel is installed, these scripts will remove the old files and UEFI entries and replace it with the new ones. Only with a new major version (for instance 6.4 becomes 6.5) then new files and entries will be created.
- You still need to clean old kernels from time to time with `vkpurge`. The remove-scripts here are used by vkpurge.
- Before this automation works as intended you have to manually set your preferred order with `efibootmgt -o x,y,z`. This is also true for every **new** kernel you install.

---
title: Installing Fedora on Developerbox
permalink: /documentation/enterprise/developerbox/installation/fedora.html
---
# Table of Contents

   * [Downloading install media](#downloading-install-media)
   * [Quirks and workarounds](#quirks-and-workarounds)
      * [[Hardware] Enable PCIe transaction size workaround](#hardware-enable-pcie-transaction-size-workaround)
      * [[First boot] Disable splash screen](#first-boot-disable-splash-screen)
      * [[First boot] Enable GT-710 clock mode workaround](#first-boot-enable-gt-710-clock-mode-workaround)

<!-- Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc) -->

# Downloading install media

The following installer images are suitable for use on your Developerbox:

 * [Fedora Server
 (DVD)](https://download.fedoraproject.org/pub/fedora/linux/releases/30/Server/aarch64/iso/Fedora-Server-dvd-aarch64-30-1.2.iso)
 * [Fedora Server
 (netinstall)](https://download.fedoraproject.org/pub/fedora/linux/releases/30/Server/aarch64/iso/Fedora-Server-netinst-aarch64-30-1.2.iso)

After downloading an installer image, please carefully study the list of
platform quirks and workarounds below. If you need further information
to complete the install process then there is a Developerbox specific
guide to [Booting an operating system installer](./) which should be
read together with the [Fedora User
Documentation](https://docs.fedoraproject.org/en-US/docs/).

These quirks and workarounds assume the `O/S Hardware Description`
firmware option is set to its default setting (`<Device Tree>`).

## [Install, First boot] PCIe transaction size workaround

The current revision of SC2A11 contains PCIe bus integration logic that
does not correctly split certain types of bus transaction. These
transactions are commonly initiated to PCIe devices with on board RAM,
such as graphics cards. The Developerbox firmware is able to partially
workaround this issue by using the virtualization hardware to prohibit
certain types of memory mapping although this workaround interferes with
some low level activity by the installer meaning it must be handled
carefully during installation.

See the [Known
Issues](../support/known-issues.md##64-bit-pcie-transactions) for more
details.

The firmware workaround is controlled by `DSW3-2`. Ensure this DIP
switch is in the **off** position before starting the installer.
To ensure the installer runs correctly we can disable the accelerated
graphics driver:

 * Wait for the grub menu to appear, then press **e** to edit the
   commands before booting
 * Use the editor to add `modprobe.blacklist=nouveau` to the kernel command line (this
   is the line commencing with the word `linux`)
 * Once all kernel arguments are updated, boot the kernel by pressing **Ctrl-X**.

Once the installer has completed it is safe to enable the firmware
workaround (which will enable 3D graphics support). Ensure the `DSW3-2`
DIP switch is in the **on** position before booting for the first time.

## [First boot] Disable splash screen

The current revision of SC2A11 contains PCIe bus integration logic that
does not correctly split certain types of bus transaction. These
transactions are commonly initiated to PCIe devices with on board RAM,
such as graphics cards. The Developerbox firmware is able to partially
workaround this issue but it is still necessary to disable the Ubuntu
splash screen.

On first boot:

 * Press **Shift** once the `Press ESCAPE for boot options` message
   appears, this will bring up the grub menu
 * Press **e** to edit the commands before booting
 * Use the editor to remove `rhgb` from the kernel command line (this
   is the line commencing `linux  /boot/vmlinuz...`)
 * Check this document for any other *[First boot]* workarounds. Once
   all kernel arguments are updated, boot the kernel by pressing
   **Ctrl-X**.

To make the change permanent:

 * Wait for Fedora to boot, then login and start a Terminal application
 * Edit `/etc/default/grub` as root, for example: `sudo vi /etc/default/grub`
 * Remove `rhgb` from `GRUB_CMDLINE_LINUX`
 * Save the file and close the editor
 * Run `sudo update-grub` to regenerate the grub config files

## [First boot] Enable GT-710 clock mode workaround

The GT-710 graphics card provided in the kit may occasionally freeze.
This is a generic nouveau driver issue that affects many different
kernel versions across both x86 and Arm platforms. It can be worked
around by passing a config option to the nouveau driver.

On first boot:

 * Press **Shift** once the `Press ESCAPE for boot options` message
   appears, this will bring up the grub menu
 * Press **e** to edit the commands before booting
 * Use the editor to add `nouveau.config=NvClkMode=auto` to the kernel
   command line (this is the line commencing `linux /boot/vmlinuz...`)
 * Check this document for any other *[First boot]* workarounds. Once
   all kernel arguments are updated, boot the kernel by pressing
   **Ctrl-X**.

To make the change permanent:

 * Wait for Ubuntu to boot, then login and start a Terminal application
 * Edit `/etc/default/grub` as root, for example: `sudo nano /etc/default/grub`
 * Add `nouveau.config=NvClkMode=auto` from `GRUB_CMDLINE_LINUX
 * Save the file and close the editor
 * Run `sudo update-grub` to regenerate the grub config file

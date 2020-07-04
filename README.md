# Firmware Modification for Embedded Linux Devices

## Direct Access

With some luck, there are recoverable procedures to deploy new images.

### SPI Flash

If there is a dedicated SPI flash holding an embedded Linux image, these are the
possible cases for in-circuit programming:

- the flash cannot be recognized by a programmer at all
- the flash is recognized, but not isolated, and the rest of the board powered,
   * and the SoC's boot ROM is accessing it constantly
   * or the SoC's boot ROM leaves intervals for full read/reflash access
- the flash can be accessed directly without interfering with the circuit around

From top to bottom, there a several options to go ahead. For the last case, a
SPI programmer can just flash a new image, for example with the Sipeed LicheePi
Nano development board. With some hardware skills, desoldering the chip is the
easiest way to go ahead. The next step is to read out the current image, and do
so multiple times to make sure it worked correctly. Verify by comparing
checksums and running a tool like `binwalk` over it to see if it finds
meaningful content. It is good to keep this for reference since it is often not
fully available otherwise. IoT devices often only upgrade certain parts like a
squashfs. More on that later.

### Bootloader

In some cases, bootloaders are accessible through various means. Mind that there
are multiple stages including the SoC/MCU's boot ROM, quite often U-Boot on the
SPI flash, and possibly others.

Each may offer access via

- a serial port, only useful if you can interrupt and get a shell
- special flags on a USB storage or SD card etc
  * some IP camera / network video recorder SoCs offer it
- shorting special pins to choose to boot from USB, SD card etc
  * some Marvell SoCs have this in their docs
- network, such as TFTP with U-Boot

## Indirect Access

If direct access is not granted, there are multiple channels allowing to read
out and/or rewrite an image. **Bear in mind that these can easily brick the
device if no access is possible otherwise.**

### Upgrade via Web UI

Some boards may offer uploading arbitrary images for upgrades such as some of
the low-cost home routers. This is frequently leveraged by the OpenWrt project.

Without other access to the existing image for investigation, this does not yet
allow for kicking of a new image since it is hard to guess what it would need to
look like. Check if the vendor offers full upgrade images for reference.

### Auto Upgrade Interception

The Wireshark tool allows for sniffing on web traffic. It may reveal regular
checks for upgrades which are deployed in various ways. Ever so often, there is
a public cloud service returning URLs that hold upgrade images.

### Shell Access

A quick scan via `nmap`, some research on the web etc may yield open Telnet or
even SSH services. In many cases, there are simple to no protections such as
`root`/`123456` logins.

### Vulnerability Exploits

As a last resort, search for what other people have figured out about the target
device. There may be weakly protected network services, various web interface
bugs such as path traversals or RCE. You might even find one yourself that is
suitable to gain you shell access.

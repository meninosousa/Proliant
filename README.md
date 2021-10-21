# The ProLiant NAS Adventure

## The Finding

On one of my lastest dumpster diving, I found this:

image of server

This is an HP ProLiant MicroServer and, until i finally decided to keep it, several toughts passed though my mind:

1. AH, HP strikes again, nothing standard is inside so let me just check if there's any hard drive and i'll burn for sure this trash;
2. Wait a second, there might be more interesting components inside so at least i'll open it just to check and then trash with it;
3. Guys, I'm running out of space on my NASPi, so I can either:
   1. I buy 2 new external HDD's (2.5 inch) to replace the existing ones and I keep using the NASPi;
   2. Or I buy the less expensive 3.5 inch ones and I used them in my new toy. It should not be that hard to adapt a Raspberry Pi or any other SBC in this case and an adapter for the cables between the HDD's and the SBC. It has nice leds so I can program them too.
4. Maybe, just maybe, HP did a good job this time and it's just a matter of replacing a fuse or something similarly easy. Might as well try it for fun, but i'll not keep it...probably;
5. OK I'll keep it until i decide what to do.

## The Specs

After digging around the HP's website, I found the model of this microserver and its corresponding specs:

### HP ProLiant MicroServer N36L

- **Processor:** AMD Athlon II Processor Model Neo N36L (1.30 GHz, 15W, 2MB)
- **Cache Memory:** 2x 1MB Level 2 cache
- **Chipset:** AMD RS785E/SB820M
- **Memory:** PC3-10600E unbuffered DDR3 ECC operating at 800MHz. Maximum 8 GB
- **Network Controller:** Embedded NC107i PCI Express Gigabit Ethernet Server Adapter
- **Expansion Slots:**
  1. PCI-e x16
  2. PCI-e x1
- **Storage Controller:** Integrated SATA controller with embedded RAID (0, 1)
- **Interfaces:**
  1. **Graphics:** On board VGA
  2. **USB 2.0 Ports:** 7 total (2 rear, 4 front panel, 1 internal)
  3. **Network:** RJ-45 10/100/1000 bits/s
  4. **Other:** eSATA Gen 2
- **Graphics:** 128MB shared supporting 1920x1200 @ 60Hz
- **Form Factor:** Ultra Micro Tower
- **Industry Standard Compliance:**
  1. ACPI V2.0 Compliant
  2. PCI 2.3 Compliant
  3. PXE Support
  4. WOL Support
  5. IPMI 2.0 compliant
  6. USB 2.0
  7. SATA Gen 2

But

- **Max Capacity per Drive:** 2 TB

With the exception of the last point, one first look at these specs and actually this is not a bad computer at all and that last point, I had a theory on that.
The fact that is AMD gives it cpu points for linux compatibility, unlike all those NAS with a proprietary OS that after a certain number of years stops receiveing updates.
The PCI-e x16 was actually populated with an AMD graphics card so it could be nice as a render farm or anything else that could use the GPU.
Ther are other small details that interested me, like WOL and Gigabit ethernet but you get the point, at this stage this was actually becomming a problem.

I was starting to be interested in this computer as a whole and not just for pieces. The problem? It wouldn't boot.


## The First Problem

where boot jpg

The computer wouldn't boot, so trash with it, forget about it.
For sure this is a non standard PSU, with the usual stupid HP proprietary connectors...
What's that? ATX 24 Pin? WHAT?

Seriously, I couldn't believe my eyes, this computer actually had a 24 Pin ATX connector for the PSU.
I dont't know if the PSU is actually standard, but at least seeing a known connector actually made a tear drop from my eye.

So I know this connector, so i know that I can test if this PSU is working by shorting the green cable:

image of psu short

Credits to SilverStone[^psushort]

[^psushort]: [from SilverStone website](https://www.silverstonetek.com/downloads/QA/PSU/PSU-Paper%20Clip-EN.pdf)

And predictably, the PSU was dead, the fan wouldn't start and using a multimeter, there was no output voltage in any 12V/5V/3.3V pin.

### An Alternative Solution

And here is where you really want to make sure that you go further down the rabbit hole or not.
In my case, I went to look for a standard ATX PSU just to see if the computer would at least give a sign of life.

And it did.

image of spashscreen

I was too deep to go back, how hard could it be to find a compatible PSU with this case? Last case scenario would be to leave a standard ATX PS/2 PSU outside of the case, right?

NO, this was a strandard FLEX PSU. What is that? Went to search all the PSU standards around and there it was:

PSU PIC JPG

>**Flex ATX**
>
>This form factor is often used in small computer cases and is sometimes wrongly referred to as “Mini-ITX” PSU.
>
>Standard dimension for this form factor is: 81.5mm (W) x 40.5mm (H) x 150mm (D)
>
>There are variations for this form factor that have longer depth. 

Credits to SilverStone again[^psuformfactors]

[^psuformfactors]: [from SilverStone website](https://www.silverstonetek.com.tw/techtalk_cont.php?tid=10055)

### The Final Solution

At 50 CHF (more or less the same in Euros or Dollars), I ended up buying the Intertech Flex AP-MFATX25P8. It has:

- **Output:** 250Watt
- **Mainboard 20+4Pin:** 1x
- **4Pin 12V:** 1x
- **IDE 4Pin (Molex):**	2x
- **S-ATA:** 3x
- **Floppy:** 1x

At 250W (100W more than the stock and dead one), this PSU should last for some time, even more consedering that I will not do anything more than store data in 2 HDD's.
I need 2 more Molex connectors, it's just a question of finding some SATA -> Molex adapters. The remaining connectors might be used on the ODD tray in the future.

So yes, I've reached a point where I actually spent money on this...at least it's standard. After receiving the PSU, found some old RAM (2x2GB), assembled everything and, let's boot it and install an OS, it will not be that hard...right?

## The Second Problem

where OS JPEG

We all heard it at some point: **Linux is everywhere and it runs in everything.**
This server supports windows but i really wanted to keep using linux around the house.
Contrary to the NASPi, the only interaction that i can have with this server, is the power button (for now).
This makes things like backup, disk information, usb copy and other scripts used on the NASPi, harder to use here, unless i use SSH all the time.
A simpler solution would be better, a solution that i knew already from previous tests. That solution is OpenMediaVault because:

- Open Source
- Maintained
- WebUI
- Low requirements

There's a strange USB internal port on this motherboard, so instead of using one of the 4 trays for the OS (and a HDD/SSD) or the ODD tray with some adapters for this tiny OS, I decided to install it on a small USB flash drive (4-16 GB). This will be easy, quoting the documentation[^omvusbinstall]:

>**Installation on USB**
>
>openmediavault can be installed on an USB flash drive to boot from it.
> 1. Follow the Install on Dedicated Drive and select your USB flash drive as target drive.
> 2. Enable the openmediavault-flashmemory plugin. This lowers the amount of writes to the USB flash drive, making sure you can enjoy it for a long time.

[^omvusbinstall]: [from OMV documentation](https://openmediavault.readthedocs.io/en/5.x/installation/on_usb.html)

After several attempts with different USB drives, both as source and as instalation drive, i only got this:

error screen JPEG

And i wasn't the only one. I saw several users reporting the same error with the same and different computers. to quote






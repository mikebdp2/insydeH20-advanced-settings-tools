# insydeH20-advanced-settings-tools
tools needed to access advanced settings that are otherwise not shown in the firmware's GUI.

This system allows to modify board settings otherwise kept hidden in vendor BIOS interfaces. It is suitable for systems that have signed images, which will refuse to boot a modded BIOS where they are shown again.

Credit for the idea goes to Falseclock user in this forum (I took it from there, anyway) https://www.bios-mods.com/forum/Thread-READ-FIRST-Access-Advanced-settings-through-EFI-shell

This tool and procedure was tested on a HP Envy 15 ah150sa, and it has quite a bit of interesting stuff hidden in there.

I add here a modified grub binary, the patch to create it, and the full blog page of the blog with the instructions.
This is of course not my work, and I'm only adding it here because the links to the file and source patch in the blog are dead.

Instructions 
Put this EFI application (source code: patch to GRUB2-1.96+20090709 [not required to run the EFI application]) on an USB-stick that is formated with FAT32 into the following directory:\EFI\BOOT\

Disable SecureBoot and boot the USB stick in EFI mode.

If everything goes well, you should see the following message> 
Welcome to GRUB!>> 
Entering rescue mode...> 
error: file not found> 
grub rescue>

You can now use the utility by writing setup_var

After you press ENTER, a license text should inform you about the risks. 
More importantly, you should see at the bottom that the tool is looking for the Setup variable and found it. The GUID should match the expected GUID, if it does not, don't continue!

The tool has this syntax:

Usage: setup_var offset [setval]

so you can use it to read the current value if you write the offset only, or to change the value if you write the offset AND the value.

After you have done, turn off the PC by pressing power button until it shuts down, and then remove usb stick.

The list of values can be dumped from the UEFI module called "SetupUtility", by using https://github.com/donovan6000/Universal-IFR-Extractor

You will need another tool to decompress and break up your firmware image into UEFI modules. I used "Tool to Insert/Replace SLIC in Phoenix / Insyde / Dell / EFI BIOSes" from mydigitallife forums.
Select the flashable firmware file with "Original BIOS" and then let it do its work, after it has finished click on Structure button in the lower left side of window.
Start opening all submenus to see a list of modules. Look for a module called "DXE Driver {some-code-here} "SetupUtility", look at the ID number within brackets.

Now open the "DUMP" folder created by the tool (in the same place you have also the bios file), find the files that have the same ID number in their name and try to read them with the IFR extractor, one of them is the right one and will yeld a text file with a list of human-readable option names with variable offset and possible values. (It's basically a dump of the text shown in the BIOS interface if it wasn't hidden)
For me it was in a file called FE3542FE-C1D3-4EF8-657C-8048606FF670_1223.ROM (yours will very likely differ)

For HP laptops you can obtain a readable fimrware image by using their BIOS update utility and having it make such plain bin files (advanced mode, third option). Just decompressing the update utility yelds a file that is unreadable.

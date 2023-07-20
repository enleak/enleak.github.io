# MemLabs Lab 1 - Beginner's Luck

## About Memlabs

MemLabs is an educational, introductory set of CTF-styled challenges which is aimed to encourage students, security researchers and also CTF players to get started with the field of Memory Forensics. "I'd suggest everyone use The Volatility Framework for analysing the memory images", the main tool being used is going to be Volatility. 

## Volatility Github Repository

[Download Volatilty](https://github.com/volatilityfoundation/volatility)


![image](https://github.com/enleak/enleak.github.io/assets/55566953/ced012af-c0bb-44be-b6e8-73233d09423b)

To begin with, download the `MemLabs-Lab1.7z` challenge file.


## First Flag!
"For a high level summary of the memory sample you're analyzing, use the `imageinfo` command. Most often this command is used to identify the operating system, service pack, and hardware architecture (32 or 64 bit), but it also contains other useful information such as the DTB address and time the sample was collected.

The imageinfo output tells you the suggested profile that you should pass as the parameter to --profile=PROFILE when using other plugins.

    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw imageinfo
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/66cb86cb-19a7-4559-84eb-f1501296ef6c)

The list of processes is the first thing we should look at based on the hints provided. In order to do this we need to provide the `pslist` argument. 

"To list the processes of a system, use the pslist command. This walks the doubly-linked list pointed to by PsActiveProcessHead and shows the offset, process name, process ID, the parent process ID, number of threads, number of handles, and date/time when the process started and exited".

    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw --profile Win7SP1x64 pslist
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/8cea5fab-1269-41a9-ad48-911e86e5ffdf)

We see that there is a windows command line open, let’s check if any commands were run on it via the `consoles` command. Naturally if we see that cmd.exe is/was running we next want to know what specific commands attackers typed into cmd.exe or executed via backdoors. The console argument in volatiltiy allows us to check for this, hence where we found the Base64 string. 

"Similar to cmdscan the consoles plugin finds commands that attackers typed into cmd.exe or executed via backdoors. However, instead of scanning for COMMAND_HISTORY, this plugin scans for CONSOLE_INFORMATION. The major advantage to this plugin is it not only prints the commands attackers typed, but it collects the entire screen buffer (input and output)"
     
    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw --profile Win7SP1x64 consoles
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/e797ec6f-968d-4af5-98a5-6f07373bdcc2)

Under `C:\Users\SmartNet`, we see that there is a base64 string , let’s decode it and check if it’s the first flag:
        
    echo "ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=" | base64 --decode
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/ecaaf270-c9e8-4189-9ab0-c3143035c715)

BOOM, WE SECURED THE FIRST FLAG!!

## Second Flag!

For the second flag we will refer back to the challenge description which mentions, "when the crash happened, she was trying to draw something". This hint in addition to the `mspaint.exe` process running, points us towards the right direciton.

![image](https://github.com/enleak/enleak.github.io/assets/55566953/49d9f567-dbc1-4419-8156-02fc6e2c8085)


Googling for `mspaint memory dump` takes us to a writeup for a 2020 CTF on CTFtime.org.
![image](https://github.com/enleak/enleak.github.io/assets/55566953/96967ed7-4dee-4b7a-9ff9-f92900395e02)


The writeup mentions that after dumping the memory for `mspaint.exe`, they opened it up in GIMP as "Raw image data, and set the offset up until there's some image or solid color came up on the preview" 
![image](https://github.com/enleak/enleak.github.io/assets/55566953/f2976ab2-a37e-4449-9979-ecbaf39378ba)
Source: https://ctftime.org/writeup/23198


For this section, I am using Ubuntu on Windows 11 via WSL because my Kali VM had some packages with unmet dependencies and after troubleshooting, nothing worked. With python 3 already installed in my Windows 11 VM, I decided to use Volatility 3 for this flag. 

Cheatsheet for Volatility 3: https://blog.onfvp.com/post/volatility-cheatsheet/

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.pslist
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/8b4fe9ab-d348-489b-a975-eeb6254ca353)

"To extract all memory resident pages in a process (see memmap for details) into an individual file, use the memdump command. Supply the output directory with -D or --dump-dir=DIR."

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw -o /home/enleak/Memlabs windows.memmap --deump -pid 2424
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6ed2dabe-5836-4636-b39a-fd04c360e38b)

Now that we extracted all memory resident pages in the `mspaint.exe` process into `pid.2424.dmp`, let's rename it into 
`pid.2424.data` since GIMP will automatically understand this as a raw input.

    cp pid.2424.dmp pid.2424.data
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/4a00f5a4-e228-45ad-a92b-e8b5efab0fbe)

Now, let's set the Image type to `RGP Alpha` and play around with the offset, width, and length in order to find our second flag.

Edit with GIMP and start digging: https://beguier.eu/nicolas/articles/security-tips-2-volatility-gimp.html


![image](https://github.com/enleak/enleak.github.io/assets/55566953/1edc9037-17c1-46a4-9cd4-dce7629b2c9c)

The image is upside down :O

![image](https://github.com/enleak/enleak.github.io/assets/55566953/b664ab2a-def6-4c33-8bd8-78bdd8f1b923)

Let's rotate it:

![image](https://github.com/enleak/enleak.github.io/assets/55566953/2adfe59d-f591-45ff-afe6-c2ae35a8c7b1)

LETS GOOOOOO, WE GOT THE SECOND FLAG!!

## Third Flag!

"Commands entered into cmd.exe are processed by conhost.exe (csrss.exe prior to Windows 7). So even if an attacker managed to kill the cmd.exe prior to us obtaining a memory dump, there is still a good chance of recovering history of the command line session from conhost.exe’s memory. If you find something weird (using the console's modules), try to dump the memory of the conhost.exe associated process and search for strings inside it to extract the command lines."

Source: https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/memory-dump-analysis/volatility-cheatsheet

The cmdline plugin will display process command-line arguments:

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.cmdline
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/907c58fb-b5c8-4073-9031-2a6f20eee7d2)

We notice that `WinRAR.exe` appears, "WinRAR is a powerful compression, archiving and archive managing software tool".

"To find FILE_OBJECTs in physical memory using pool tag scanning, use the `filescan` command. This will find open files even if a rootkit is hiding the files on disk and if the rootkit hooks some API functions to hide the open handles on a live system. The output shows the physical offset of the FILE_OBJECT, file name, number of pointers to the object, number of handles to the object, and the effective permissions granted to the object."

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.filescan | grep Important.rar
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/49dac5a7-10ee-49fe-90c3-ac362ea235ec)

"An important concept that every computer scientist, especially those who have spent time doing operating system research, is intimately familiar with is that of caching. Files are cached in memory for system performance as they are accessed and used. This makes the cache a valuable source from a forensic perspective since we are able to retrieve files that were in use correctly, instead of file carving which does not make use of how items are mapped in memory. Files may not be completely mapped in memory (also for performance), so missing sections are zero padded. Files dumped from memory can then be processed with external tools.

The dumped filename is in the format of:

file.[PID].[OFFSET].ext

The OFFSET is the offset of the SharedCacheMap or the _CONTROL_AREA, not the _FILE_OBJECT.

The extension (EXT) can be:

img – ImageSectionObject
dat - DataSectionObject
vacb – SharedCacheMap"

Let's dump the file:

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw -o /home/enleak/Memlabs windows.dumpfiles --physaddr 0x3fa3ebc0
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/2d36fd7b-3cdb-4d66-9dfe-488d8d94903e)

Renaming the file:

    mv file.None.0xfffffa8001034450.dat Important.rar
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6ab94ca8-4dff-40d3-9124-417d831cf949)

Upon trying to open the `Important.rar` file, we notice that the NTLM hash of Alissa is needed:

    unrar e Important.rar
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/e825a7f2-4773-4063-b2fa-513849af2051)

"To extract and decrypt cached domain credentials stored in the registry, use the `hashdump` command" We  use the hashdump argument in order to dump all NTLM hashes:

    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw --profile Win7SP1x64 hashdump
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/1baf9aa1-1dd1-4643-931e-cee041295308)

Make the NTLM hash all upper case:
![image](https://github.com/enleak/enleak.github.io/assets/55566953/44e51052-2f53-4085-b5af-c3291fe9feb5)

Now that we have the password as requested, we can finally open `Important.rar` which contains the third flag!

    unrar e Important.rar
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6b82ca08-bc64-468f-9ec7-2d6399d1cf84)

Opening the `PNG` reveals the last flag!

    xdg-open flag3.png
    
![image](https://github.com/enleak/enleak.github.io/assets/55566953/5af18c1f-9274-415e-9c86-d0533ac2ec96)

LETS GOOOOOOOOOOO!!

## Resources:
+ [CTFTime Writeup](https://ctftime.org/writeup/23198)
+ [Introduction to Memory Forensics - Digital Forensics Course](https://youtu.be/1PAGcPJFwbE)
+ [Volatility Cheatsheet by Ashley](https://blog.onfvp.com/post/volatility-cheatsheet/)
+ [HackTricks Volatility Cheatsheet](https://book.hacktricks.xyz/generic-methodologies-and-resources/basic-forensic-methodology/memory-dump-analysis/volatility-cheatsheet)
+ [Volatility Command Reference](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference)





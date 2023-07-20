# MemLabs Lab 1 - Beginner's Luck

### About Memlabs
MemLabs is an educational, introductory set of CTF-styled challenges which is aimed to encourage students, security researchers and also CTF players to get started with the field of Memory Forensics. "I'd suggest everyone use The Volatility Framework for analysing the memory images", the main tool being used is going to be Volatility. 

### Download Volatilty: https://github.com/volatilityfoundation/volatility


![image](https://github.com/enleak/enleak.github.io/assets/55566953/ced012af-c0bb-44be-b6e8-73233d09423b)

To begin with, download the "MemLabs-Lab1.7z" challenge file.

    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw imageinfo
![image](https://github.com/enleak/enleak.github.io/assets/55566953/66cb86cb-19a7-4559-84eb-f1501296ef6c)

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.pslist
![image](https://github.com/enleak/enleak.github.io/assets/55566953/96bc37ae-d2a2-4bf8-8f4d-27ffcfb89ac2)



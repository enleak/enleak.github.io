# MemLabs Lab 1 - Beginner's Luck

### About Memlabs
MemLabs is an educational, introductory set of CTF-styled challenges which is aimed to encourage students, security researchers and also CTF players to get started with the field of Memory Forensics. "I'd suggest everyone use The Volatility Framework for analysing the memory images", the main tool being used is going to be Volatility. 

### Download Volatilty: https://github.com/volatilityfoundation/volatility


![image](https://github.com/enleak/enleak.github.io/assets/55566953/ced012af-c0bb-44be-b6e8-73233d09423b)

To begin with, download the "MemLabs-Lab1.7z" challenge file.

    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw imageinfo
![image](https://github.com/enleak/enleak.github.io/assets/55566953/66cb86cb-19a7-4559-84eb-f1501296ef6c)

## First Flag!

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.pslist
![image](https://github.com/enleak/enleak.github.io/assets/55566953/96bc37ae-d2a2-4bf8-8f4d-27ffcfb89ac2)

We see that there is a cmd console open, let’s check if any commands were run on it via the consoles command. Naturally if we see that cmd.exe is/was running we next want to know what specific commands attackers typed into cmd.exe or executed via backdoors. The console flag in Volatiltiy allows us to check for this, hence where we found the Base64 string. 
     
    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw --profile Win7SP1x64 consoles
![image](https://github.com/enleak/enleak.github.io/assets/55566953/e797ec6f-968d-4af5-98a5-6f07373bdcc2)

We see that there is a base64 string , let’s decode it and check if it’s the first flag:
        
    echo "ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=" | base64 --decode
![image](https://github.com/enleak/enleak.github.io/assets/55566953/ecaaf270-c9e8-4189-9ab0-c3143035c715)

## Second Flag!

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.pslist
![image](https://github.com/enleak/enleak.github.io/assets/55566953/56a835d6-b52e-4d33-bca5-82d7c8b4a0ae)

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw -o /home/enleak/Memlabs windows.memmap --deump -pid 2424
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6ed2dabe-5836-4636-b39a-fd04c360e38b)

    cp pid.2424.dmp pid.2424.data
![image](https://github.com/enleak/enleak.github.io/assets/55566953/4a00f5a4-e228-45ad-a92b-e8b5efab0fbe)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/654e58d3-bdbf-4ecd-a680-b6b6b68df3f5)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/2adfe59d-f591-45ff-afe6-c2ae35a8c7b1)












# MemLabs Lab 1 - Beginner's Luck

### About Memlabs
MemLabs is an educational, introductory set of CTF-styled challenges which is aimed to encourage students, security researchers and also CTF players to get started with the field of Memory Forensics. "I'd suggest everyone use The Volatility Framework for analysing the memory images", the main tool being used is going to be Volatility. 

Download Volatilty: https://github.com/volatilityfoundation/volatility


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

![image](https://github.com/enleak/enleak.github.io/assets/55566953/49d9f567-dbc1-4419-8156-02fc6e2c8085)

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.pslist
![image](https://github.com/enleak/enleak.github.io/assets/55566953/56a835d6-b52e-4d33-bca5-82d7c8b4a0ae)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/f2976ab2-a37e-4449-9979-ecbaf39378ba)
Source: https://ctftime.org/writeup/23198

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw -o /home/enleak/Memlabs windows.memmap --deump -pid 2424
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6ed2dabe-5836-4636-b39a-fd04c360e38b)

    cp pid.2424.dmp pid.2424.data
![image](https://github.com/enleak/enleak.github.io/assets/55566953/4a00f5a4-e228-45ad-a92b-e8b5efab0fbe)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/1edc9037-17c1-46a4-9cd4-dce7629b2c9c)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/caed12c0-1073-422a-88ab-fc39568d0c90)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/654e58d3-bdbf-4ecd-a680-b6b6b68df3f5)

![image](https://github.com/enleak/enleak.github.io/assets/55566953/2adfe59d-f591-45ff-afe6-c2ae35a8c7b1)

## Third Flag!

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.cmdline
![image](https://github.com/enleak/enleak.github.io/assets/55566953/907c58fb-b5c8-4073-9031-2a6f20eee7d2)

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw windows.filescan | grep Important.rar
![image](https://github.com/enleak/enleak.github.io/assets/55566953/49dac5a7-10ee-49fe-90c3-ac362ea235ec)

    python3 vol.py -f ../../MemLabs/MemoryDump_Lab1.raw -o /home/enleak/Memlabs windows.dumpfiles --physaddr 0x3fa3ebc0
![image](https://github.com/enleak/enleak.github.io/assets/55566953/2d36fd7b-3cdb-4d66-9dfe-488d8d94903e)

    mv file.None.0xfffffa8001034450.dat Important.rar
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6ab94ca8-4dff-40d3-9124-417d831cf949)

    unrar e Important.rar
![image](https://github.com/enleak/enleak.github.io/assets/55566953/e825a7f2-4773-4063-b2fa-513849af2051)

    python vol.py -f ../../MemLabs/MemoryDump_Lab1.raw --profile Win7SP1x64 hashdump
![image](https://github.com/enleak/enleak.github.io/assets/55566953/1baf9aa1-1dd1-4643-931e-cee041295308)

Make the NTLM hash all upper case:
![image](https://github.com/enleak/enleak.github.io/assets/55566953/44e51052-2f53-4085-b5af-c3291fe9feb5)

    unrar e Important.rar
![image](https://github.com/enleak/enleak.github.io/assets/55566953/6b82ca08-bc64-468f-9ec7-2d6399d1cf84)

    xdg-open flag3.png
![image](https://github.com/enleak/enleak.github.io/assets/55566953/5af18c1f-9274-415e-9c86-d0533ac2ec96)






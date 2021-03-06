![passage logo](https://user-images.githubusercontent.com/55566953/110192011-a3996580-7df9-11eb-8032-ddf84d0dcb95.png)

### Reconnaissance

**Scanning the network for open ports and services using `nmap -sC -sV -v -oN nmap.txt`.**
`-sC` to scan with default NSE scripts
`-sV` for service version info
`-v` for verbose mode
`-oN` to output scan 
`````
Starting Nmap 7.91 ( https://nmap.org ) at 2021-03-05 21:30 EST
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Initiating Ping Scan at 21:30
Scanning 10.10.10.206 [2 ports]
Completed Ping Scan at 21:30, 0.02s elapsed (1 total hosts)
Initiating Connect Scan at 21:30
Scanning passage.htb (10.10.10.206) [1000 ports]
**Discovered open port 22/tcp on 10.10.10.206**
**Discovered open port 80/tcp on 10.10.10.206**
Completed Connect Scan at 21:30, 2.09s elapsed (1000 total ports)
Initiating Service scan at 21:30
Scanning 2 services on passage.htb (10.10.10.206)
Completed Service scan at 21:30, 6.10s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.10.206.
Initiating NSE at 21:30
Completed NSE at 21:30, 2.40s elapsed
Initiating NSE at 21:30
Completed NSE at 21:30, 0.10s elapsed
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Nmap scan report for passage.htb (10.10.10.206)
Host is up (0.066s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 17:eb:9e:23:ea:23:b6:b1:bc:c6:4f:db:98:d3:d4:a1 (RSA)
|   256 71:64:51:50:c3:7f:18:47:03:98:3e:5e:b8:10:19:fc (ECDSA)
|_  256 fd:56:2a:f8:d0:60:a7:f1:a0:a1:47:a4:38:d6:a8:a1 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Passage News
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Initiating NSE at 21:30
Completed NSE at 21:30, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.26 seconds

`````

## Enumerating Port 80

**Visiting the site http://$IP greets us with a "Passage News" page.**
**Once logged in we see the Passage News page that contains a bunch of gibberish except the only comment made by admin mentioning something called "Fail2Ban".**
![passage](https://user-images.githubusercontent.com/55566953/110192348-76e64d80-7dfb-11eb-9759-2bf66c9d14ec.PNG)

**Reading the comment let's us know that Fail2Ban "bans an IP for 2 mins due to excessive requests".**
![comment](https://user-images.githubusercontent.com/55566953/110192399-e3614c80-7dfb-11eb-9046-d3d893de7d9e.PNG)

**Googling Fail2Bin tells us that `"Fail2Ban is an intrusion prevention software framework that protects computer servers from brute-force attacks".`**
*Some more information regarding Fail2Bin found on their site: https://www.fail2ban.org/wiki/index.php/Main_Page*
![fail2bin](https://user-images.githubusercontent.com/55566953/110192591-08a28a80-7dfd-11eb-9f94-57e096e5d3a4.PNG)

**This let's us know that a Gobuster scan will not work. However, we try it anyways using `gobuster dir -u http://$IP/index.php -w /opt/directory-list-2.3-medium.txt --wildcard -x php` and the connection is refused as expected.**
![bt](https://user-images.githubusercontent.com/55566953/110192984-63d57c80-7dff-11eb-9ae7-eca118bfe480.PNG)

**Knowing all this information hints that there might be some sort of login page on this machine.**
**Now let's open up OWASP Zap and paste the URL into the Spider tool which "is used to automatically discover new resources/URLs on your website".**
![zap](https://user-images.githubusercontent.com/55566953/110192781-40f69880-7dfe-11eb-9c01-f7ee36aab465.PNG)

**The scan results greet us with a whole list of URLS, one in particular looks very interesting; `"CuteNews".`**
![zapr](https://user-images.githubusercontent.com/55566953/110192852-aba7d400-7dfe-11eb-86a9-29266d3fa9ae.PNG)

## CuteNews Web-Page

**Visiting `http://$IP/CuteNews/` shows us a login/registration page!**
![cutenews](https://user-images.githubusercontent.com/55566953/110193160-45bc4c00-7e00-11eb-963d-a5261cee3b47.PNG)

**Registering for an account gives us access to the "Cute News Management System"**
![mana](https://user-images.githubusercontent.com/55566953/110193223-d98e1800-7e00-11eb-8880-e42e4ff78172.PNG)

**The footer of the web page reveals the version of the CuteNews service running, let's google it and see if there are any known vulnerabilities/exploits.**

![2 1 2](https://user-images.githubusercontent.com/55566953/110193411-b3b54300-7e01-11eb-9650-1ccf20158af0.PNG)

















































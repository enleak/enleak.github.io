![passage logo](https://user-images.githubusercontent.com/55566953/110192011-a3996580-7df9-11eb-8032-ddf84d0dcb95.png)

### Reconnaissance

 **Scanning the network for open ports and services using `nmap -sC -sV -v -oN nmap.txt`.**
 `-sC` to scan with default NSE scripts,
 `-sV` for service version info,
 `-v` for verbose mode,
 `-oN` to output scan. 
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

## CuteNews Web-Page Discovery

**Visiting `http://$IP/CuteNews/` shows us a login/registration page!**

![cutenews](https://user-images.githubusercontent.com/55566953/110193160-45bc4c00-7e00-11eb-963d-a5261cee3b47.PNG)

**Registering for an account gives us access to the "Cute News Management System Dashboard".**

![mana](https://user-images.githubusercontent.com/55566953/110193223-d98e1800-7e00-11eb-8880-e42e4ff78172.PNG)

**and "Personal Options/General Options".**

![php](https://user-images.githubusercontent.com/55566953/110193750-db0d0f80-7e03-11eb-9622-d021e12207d8.PNG)


**The footer of the web page reveals the version of the CuteNews service running, let's google it and see if there are any version exploits for `CuteNews 2.1.2`.**

![2 1 2](https://user-images.githubusercontent.com/55566953/110193411-b3b54300-7e01-11eb-9650-1ccf20158af0.PNG)

## Scouting for Vulnerability 

**A ["CuteNews 2.1.2 Remote Code Execution Vulnerability"](https://musyokaian.medium.com/cutenews-2-1-2-remote-code-execution-vulnerability-450f29673194) Medium Article was found explaining the exploit in depth.**

![medium](https://user-images.githubusercontent.com/55566953/110194165-12c88700-7e05-11eb-9157-b832a9fe4cd3.PNG)

**"No matter how big the uploaded file is it doesn’t check on the size of the file this allows an attacker to use large files like a PHP reverse shell file”. Based on this, we're going to upload a php reverse shell. Pentestmonkey has a [php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) ready for us to use!**

## Reverse Shell

**This php reverse shell will be saved to a file named `shell.php` with the modified `$IP` and `$PORT`.**
*`$IP = Your VPN's IP Address` (Your tun0, find it by executing `ifconfig tun0` on your local machine).*
*`$PORT = Port you want to listen on.`*

![jjjj](https://user-images.githubusercontent.com/55566953/110197314-c71fd880-7e18-11eb-8831-586b18adf17a.PNG)

**Before uploading the Reverse Shell we need to take a closer look at the medium article that was mentioned earlier, we'll see that CuteNews uses magic bytes to validate the type of file being uploaded (magic byte is nothing but the first few bytes of a file which is used to recognize a file).**
**"GIF8; which are the magic bytes of a GIF image" will be edited into the PHP Reverse Shell in order to make is seem like it's a harmless gif file.**

![gig](https://user-images.githubusercontent.com/55566953/110197714-6d6cdd80-7e1b-11eb-9447-2ba85440661c.PNG)

**We can intercept the POST request using a tool called** [BurpSuite](https://portswigger.net/burp/documentation/desktop/penetration-testing) **and add the `GIF8` magic byte into the Reverse Shell.**

![burp](https://user-images.githubusercontent.com/55566953/110197892-7f02b500-7e1c-11eb-8d1b-ab243fde5511.PNG)

**We get a notification letting us know that the upload was successful!**

![success](https://user-images.githubusercontent.com/55566953/110197923-af4a5380-7e1c-11eb-85e9-1d23636424f0.PNG)

**Let's set uup a netcat listener using the same `$PORT` we specified for the PHP Reverse Shell using the command `nc -nvlp $PORT`**

![nc](https://user-images.githubusercontent.com/55566953/110198044-a1490280-7e1d-11eb-8caf-9401729c3875.PNG)

**Now I manually head to where the shell was uploaded, in this case; `/uploads/shell.php` and we get a shell!!**

![id](https://user-images.githubusercontent.com/55566953/110198104-06045d00-7e1e-11eb-9853-46fb97ce7d23.PNG)


**It's always good measure to stabalize a shell! [RopNop Blog](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/) states that "shells have other shortcomings as well:**

 1. some commands, like su and ssh require a proper terminal to run
 2. STDERR usually isn’t displayed
 3. Can’t properly use text editors like vim
 4. No tab-complete
 5. No up arrow history
 6. No job control
 7. Etc…"

**On his blog we can also find a way to use Python to "to spawn a pty. The pty module let’s you spawn a psuedo-terminal that can fool commands like su into thinking they are being executed in a proper terminal." The command used is;**

![python](https://user-images.githubusercontent.com/55566953/110198200-bbcfab80-7e1e-11eb-817f-626be7ac91bb.PNG)

**Once executed, it'll look like this;**

![data](https://user-images.githubusercontent.com/55566953/110198235-f89ba280-7e1e-11eb-9f6f-e60e75451ecf.PNG)

## WWW-Data@Passage

**Now that this shell is stable lets do some enumeration and look for interesting files that could eventualy help us get root. The first directory I look at is the `/var` directory. Why? This directory contains variable data files, email-in-boxes, web application related files, cron files, and more. In this case, I looked for a CuteNews directory containing all the important web application files (searching for credentials to move either horizontally or vertically).**

## Base 64 Encoded Data

**After looking around for a while, I found myself a `/users` directory located in `/var/www/html/CuteNews/cdata/users`. In this directory we were able to find some base 64 encoded data!?**

![HASH](https://user-images.githubusercontent.com/55566953/110198871-8083ab80-7e23-11eb-9637-4928b904dbc0.PNG)

**In order to decode this encoded data we use the `echo` command. The command is `echo $base64string | base64 -d`, you can find this at [How can I decode a base64 string from the command line?](https://askubuntu.com/questions/178521/how-can-i-decode-a-base64-string-from-the-command-line).**

![decode](https://user-images.githubusercontent.com/55566953/110199098-a3628f80-7e24-11eb-9ca2-c2af38d334c8.PNG)

**How do we know which user(s) we need the credentials for? While browsing earlier we also ran into a directory with two users in `/var/lib/AccountsService/users`;**

![users](https://user-images.githubusercontent.com/55566953/110199205-47e4d180-7e25-11eb-835e-c4d7e1912735.PNG)


## HashCat

**The user, email, and passowrd hash was stored in this data! Let's grab the hash and use [`hash-identifier`](https://tools.kali.org/password-attacks/hash-identifier)( a tool that identifies different types of hashes).**

![sha](https://user-images.githubusercontent.com/55566953/110199545-ddcd2c00-7e26-11eb-828e-60a5fe837561.PNG)

**The hash was identified as a `SHA-256` hash which can be cracked using a neat tool called [hashcat](https://hashcat.net/hashcat/) (password cracking tool) in order to retrieve the password in clear text.**

**Before we crack the password, hashcat requires some input in order to function properly** ,we need the;
1. Attack mode(-a) - For attack mode we use 0 or straight which (is trying all words in a list).
2. Hash mode(-m) - For Hash mode we see that [Hashcat Example Hashes](https://hashcat.net/wiki/doku.php?id=example_hashes) shows 1400 which represents SHA-256.
3. hash - The hash you want cracked
4. passowrd list- Any password 

**The command used was `sudo hashcat -a 0 -m 1400 hash /opt/rockyou.txt`, we were able to retrieve the clear text password!!**

![cracked](https://user-images.githubusercontent.com/55566953/110199991-62b94500-7e29-11eb-8a97-fabad1d2c413.PNG)

## Paul@Passage

**Using these credentials we're able to `su`**

![paul](https://user-images.githubusercontent.com/55566953/110200096-233f2880-7e2a-11eb-8c49-7eb5df46ac4c.PNG)

**Now we `cd` back into pauls home directory and use `ls -la` which list all files, even hidden files (hidden files which look like “.file”). We are able to see a `.ssh` directory which could contain ssh private keys used to login to other users on a machine (via ssh/port 22 whcih was open if you look at the nmap scan at the beginning of this
writeup). Now let's `cd` into `.shh` and `ls` to list all the files.**

![idr](https://user-images.githubusercontent.com/55566953/110200275-1838c800-7e2b-11eb-97a7-b3b85b1a425a.PNG)

**"The authorized_keys file in SSH specifies the SSH keys that can be used for logging into the user account for which the file is configured". Knowing this we `cat` the `authorized_keys` file and see that the private key belongs to the user `navad`.**

![navad](https://user-images.githubusercontent.com/55566953/110200439-16bbcf80-7e2c-11eb-93c2-2cf4e200e334.PNG)

**We can authenticate as user nadav by getting a hold of the private key (which we have). The private key or `id_rsa` looks like this;**

![id_](https://user-images.githubusercontent.com/55566953/110200591-c729d380-7e2c-11eb-9e01-33ba36da2566.PNG)

**We now save this private key into a file in our local system and name it `nadavkey`. I gave the “id_rsa” file “600” or “rw” permissions by using `chmod 600 navadkey`.**

## Nadav@Passage

**Successfully logged in as nadav!**

![shh](https://user-images.githubusercontent.com/55566953/110213203-fa8c5280-7e6c-11eb-8aaa-a5644040737b.PNG)

## Privilage Escalation
 
**There's a nice tool that might help us escelate privilages called [Linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) (tool that searches for possible local privilege escalation paths that you could exploit and print them to you with nice colors so you can recognize the misconfigurations easily). To transfer files from our local linux machine to ssh instance (remote machine) we can use `updog` to spawn a local http webserver (`127.0.0.1:Port`), What is updog? “Updog is a replacement for Python’s SimpleHTTPServer. It allows uploading and downloading via HTTP/S, can set ad hoc SSL certificates and use HTTP basic auth”.**

**To execute the file transfer;**

![linlin](https://user-images.githubusercontent.com/55566953/110213967-6623ef00-7e70-11eb-89d8-aa723cdd74e9.PNG) ![wgett](https://user-images.githubusercontent.com/55566953/110213944-455b9980-7e70-11eb-8859-81dd006961c1.PNG) 

**Now, make the `linpeas.sh` file you transferred executable by using `chmod +x linpeas.sh`.**

![+x](https://user-images.githubusercontent.com/55566953/110214050-c9ae1c80-7e70-11eb-8695-53ea55e00933.PNG)





























































































































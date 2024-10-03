https://tryhackme.com/room/crackthehash
1.1 
48bb6e862e54f2a795ffc4e541caed4d
Going to use for more info 
https://www.tunnelsup.com/hash-analyzer/



Hash:	48bb6e862e54f2a795ffc4e541caed4d
Salt:	Not Found
Hash type:	MD5 or MD4
Bit length:	128
Character length:	32
Character type:	hexidecimal


Typically have to use johntheripper with the rockyou.txt to figure out the code. 
/usr/share/wordlists/dirb/rockyou.txt location of rockyou file text. 
Having issues navigating john the ripper, https://www.youtube.com/watch?v=C9wP49Z2JBQ 
Figured it out, the command is here.
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=raw-md5 48bb6e862e54f2a795ffc4e541caed4d 
stat: 48bb6e862e54f2a795ffc4e541caed4d: No such file or directory
                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=raw-md5 /home/kali/Desktop/crackyou     
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
easy             (?)     
1g 0:00:00:00 DONE (2024-01-08 09:38) 100.0g/s 17241Kp/s 17241Kc/s 17241KC/s florian1..dynamic1
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed. 


1.2
CBFDAC6008F9CAB4083784CBD1874F76618D2A97
Hint is with sha, but unsure on which version. 
Site says 
Hash:	CBFDAC6008F9CAB4083784CBD1874F76618D2A97
Salt:	Not Found
Hash type:	SHA1 (or SHA 128)
Bit length:	160
Character length:	40
Character type:	hexidecimal

Unsure is site is cheating or not, seems too easy to figure out which hash it is.
Updatiung the hash in the crackyou word file.
──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=raw-sha1 /home/kali/Desktop/crackyou
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA1 [SHA1 256/256 AVX2 8x])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
password123      (?)     
1g 0:00:00:00 DONE (2024-01-08 09:40) 100.0g/s 138400p/s 138400c/s 138400C/s jesse..password123
Use the "--show --format=Raw-SHA1" options to display all of the cracked passwords reliably
Session completed. 



1.3
1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
Hash:	1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
Salt:	Not Found
Hash type:	SHA2-256
Bit length:	256
Character length:	64
Character type:	hexidecimal
Has some initial issues with sha256, looking up some recources apparently the format was wrong.
Unable to just type in she2 into the raw command. 
Correct command is here. 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (?)     
1g 0:00:00:00 DONE (2024-01-08 09:43) 100.0g/s 3276Kp/s 3276Kc/s 3276KC/s 123456..dyesebel
Use the "--show --format=Raw-SHA256" options to display all of the cracked passwords reliably
Session completed. 


1.4
$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom
Hash:	$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom
Salt:	Not Found
Hash type:	bcrypt
Bit length:	184
Character length:	60
Character type:	$2x$x$ followed by base64
Hash:	eUznr71EeNkJkUlypTsgbX1H68wsRom
Salt:	Dwt1BZj6pcyc3Dy1FWZ5ie

Ran into an issue with the hash, its blowfish which does not take the raw command.
When running it seems to not crack as fast as the other ones. Command currently 
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=bcrypt /home/kali/Desktop/crackyou 
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 4096 for all loaded hashes

Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:12 0.00% (ETA: 2024-01-14 23:18) 0g/s 29.53p/s 29.53c/s 29.53C/s nirvana..metallica
Its going to take too long, the hitn solidifies this
Search the hashcat examples page (https://hashcat.net/wiki/doku.php?id=example_hashes) for $2y$. This type of hash can take a very long time to crack, so either filter rockyou for four character words, or use a mask for four lower case alphabetical characters.
Found these commands in the help page 
--prince-skip=N            Initial skip
--prince-limit=N           Limit number of candidates generated
--prince-wl-dist-len       Calculate length distribution from wordlist
--prince-wl-max=N          Load only N words from input wordlist
--prince-case-permute      Permute case of first letter
--prince-mmap              Memory-map infile (not available with case permute)
--prince-keyspace          Just show total keyspace that would be produced
That’s not it, tried --max-length=4 but it crashes?
Found the issue https://www.reddit.com/r/AskNetsec/comments/98woj8/quick_question_about_john_the_ripper_technical/

└─$ sudo /root/.john/john.rec                 
[sudo] password for kali: 
sudo: /root/.john/john.rec: command not found
                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ rm /home/kali/.john/john.rec
                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ rm /home/kali/.john/john.rec
rm: cannot remove '/home/kali/.john/john.rec': No such file or directory
                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=bcrypt /home/kali/Desktop/crackyou --min-length=4
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 4096 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Max needs to be used, changed --min-length=4 to --max-length=4
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/dirb/rockyou.txt --format=bcrypt /home/kali/Desktop/crackyou --max-length=4
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 4096 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:05 0.23% (ETA: 10:39:05) 0g/s 29.56p/s 29.56c/s 29.56C/s zach..free
bleh             (?)     
1g 0:00:00:25 DONE (2024-01-08 10:03) 0.03889g/s 28.70p/s 28.70c/s 28.70C/s hugs..1369
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 



1.5
279412f945939ba78ce0758d3fd83daa
Hash:	279412f945939ba78ce0758d3fd83daa
Salt:	Not Found
Hash type:	MD5 or MD4
Bit length:	128
Character length:	32
Character type:	hexidecimal

Why md4 or mf5. Hint says md4
Having issues cracking md4. Unsure if command error or general issue with the hash. 
Another missing component it seems.
┌──(kali㉿kali)-[~]
└─$ sudo john --format=raw-md4 --wordlist=/usr/share/wordlists/dirb/rockyou.txt /home/kali/Desktop/crackyou      
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD4 [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2024-01-08 10:26) 0g/s 34150Kp/s 34150Kc/s 34150KC/s  filimani..*7¡Vamos!
Session completed. 
                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~]
Going to skip task 1.5 for now. 

Eternity22
Going to pivot to hashcat for now according to the site. 



2.1
Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
Hash:	F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
Salt:	Not Found
Hash type:	SHA2-256
Bit length:	256
Character length:	64
Character type:	hexidecimal
Hashcat command 
/tools/hashcat$ ./hashcat64.bin -m 1400 hash.txt ../rockyou.txt

f09edcb1fcefc6dfb23dc3505a882655ff77375ed8aa2d1c13f640fccc2d0c85:paule


2.2
Hash: 1DFECA0C002AE40B8619ECF94819CC1B
Hint NTLM
Hash:	1DFECA0C002AE40B8619ECF94819CC1B
Salt:	Not Found
Hash type:	MD5 or MD4
Bit length:	128
Character length:	32
Character type:	hexidecimal
This md4 or md5?
HASH: 1DFECA0C002AE40B8619ECF94819CC1B

Possible Hashs:
[+]  MD5
[+]  Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

~/tools$ ./hashcat/hashcat64.bin -m 1000 md5.hash rockyou.txt

1dfeca0c002ae40b8619ecf94819cc1b:n63umy8lkf4i


2.3 

Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.
Salt: aReallyHardSalt
Salt?

Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.
Salt: aReallyHardSalt
Rounds: 5

/tools$ ./hashcat/hashcat64.bin -m 1800 sha512.hash rockyou.txt --session sha512
hashcat (v5.1.0) starting...



Output: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:waka99



2.4

Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6
Salt: tryhackme

Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6
Salt: tryhackme
/tools/hashcat$ ./hashcat64.bin -m 110 ../hash.sha1 ../rockyou.txt 
hashcat (v5.1.0) starting...
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:481616481616

**https://cyberdefenders.org/blueteam-ctf-challenges/oski/**


Provided with a single text file that contains the following 


MD5 Hash: 12c1842c3ccafe7408c23ebf292ee3d9 


The provided Scenario 

The accountant at the company received an email titled "Urgent New Order" from a client late in the afternoon. When he attempted to access the attached invoice, he discovered it contained false order information. Subsequently, the SIEM solution generated an alert regarding downloading a potentially malicious file. Upon initial investigation, it was found that the PPT file might be responsible for this download. Could you please conduct a detailed examination of this file?

Things that may stand out:

- generated an alert regarding downloading a potentially malicious file
- PPT file might be responsible for this download.


Questions

To effectively categorize and understand the behavior and intent of this potential malware, it is crucial to identify its family. What is the name of the malware family for the malicious executable found within the PPT?

A little confused, because its just a md5 hash signature. (maybe missing info)

Virus total doesn't reach back with anything. 
Got a hit
This was found with 
"how to examine a potentially harmful md5 hash":https://malwareinstitute.org/tools/
Received 
**
🔍 Detection Rate: 60/76 engines
👾 trojan.mint/zitirez
**

Im guessing this does not work, because it only provides barebones info about the hash.

Tried to no avail
- Trojan 
- zitirez

Hint 2:

VirusTotal is an excellent resource for checking known hashes in its database. Review the Crowdsourced IDS rules under the Detection tab, which can indicate a potential malware family.

Unsure what they mean by the Detection tab, but the answer is Stealc

Short background: Stealc is an information stealer advertised by its presumed developer Plymouth on Russian-speaking underground forums and sold as a Malware-as-a-Service since January 9, 2023. According to Plymouth's statement, stealc is a non-resident stealer with flexible data collection settings and its development is relied on other prominent stealers: Vidar, Raccoon, Mars and Redline.

Question 2 

Determining the creation time of the malware can provide insights into its origin. What was the time of malware creation?

How with just the hash. 
Hint 1 
Show Hint 1
To find metadata like creation time, analyze the file’s detailed properties. Which sections typically store creation information on VirusTotal?

Hint is correct.

The answer was provided in the History tab as 

History
Creation Time
2022-09-28 17:40:46 UTC
 
First Seen In The Wild
2023-09-23 22:33:33 UTC
 
First Submission
2023-09-23 22:02:55 UTC
 
Last Submission
2024-02-16 11:04:02 UTC
 
Last Analysis
2025-02-26 11:33:41 UTC

Q3 

Identifying the command and control (C2) server that the malware communicates with can help trace back to the attacker. Which C2 server does the malware in the PPT file communicate with?

Just did some digging on the provided info page and found the http://171.22.28.221/5c06c05b7b34e8e6.php

Allot of useful info on that page including 
C2, evasion (static & dynamic) & behaviors

Q4

Identifying the initial actions of the malware post-infection can provide insights into its primary objectives. What is the first library that the malware requests post-infection?

Again digging around found me the answer. 

A few notes here

I initially looked in the Files opened and tried the first action which was 
%WINDIR%\system32\ntdll.dll

Nope 
Then 
ntdll.dll
Nope

Looked around for why that was and saw the section of Files dropped

Which provided sqlite3.dll first, which was the answer. 


Q5

Upon examining the malware, it appears to utilize the RC4 key for decrypting a base64 string. What specific RC4 key does this malware use?

Needed Anyrun to fun the RC4 within the "Malware configuration" tab. 

5329514621441247975720749009

Q6

Identifying an adversary's techniques can aid in understanding their methods and devising countermeasures. Which MITRE ATT&CK technique are they employing to steal a user's password?


https://attack.mitre.org/tactics/TA0006/
Found here and some brute force.

Also based on some of the techniques provided in the behavior tab.

T1555

Q7

Malware may delete files left behind by the actions of their intrusion activity.Which directory or path does the malware target for deletion?

Fairly easy one.

C:\ProgramData

You can see an attempt at C:\loqktcd\btwg.exe and the rest contain Program data, most likely (when lookd up) has to do with error reporting and metadata logs. 


Q8 

Understanding the malware's behavior post-data exfiltration can give insights into its evasion techniques. After successfully exfiltrating the user's data, how many seconds does it take for the malware to self-delete?

lul guessed 1 with no luck, 5 worked though. Altghough the hint shows that it can be found in the timeout info within the CMD
process. 


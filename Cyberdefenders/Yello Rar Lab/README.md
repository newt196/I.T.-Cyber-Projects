https://cyberdefenders.org/blueteam-ctf-challenges/yellow-rat/

malware hash: 30E527E45F50D2BA82865C5679A6FA998EE0A1755361AB01673950810D071C85

Random aside, its sha-256

I want to analyze, but for right now going to jump right into the questions.

Q1.

Understanding the adversary helps defend against attacks. What is the name of the malware family that causes abnormal network traffic?

...kind of dum

The question astrick gives to much insight. After some digging, we can see 
Yellow Cockatoo RAT within the community tab.


Q2. As part of our incident response, knowing common filenames the malware uses can help scan other workstations for potential infection. What is the common filename associated with the malware discovered on our workstations?

Key points within the questions.
- "common filenames the malware uses"
- This is apart of a .dll that can be scanned for evidence of the malware. 

Fairly easy here, just some digging and reasoning was used here.

The astricks dont give the answer too much.
Within virustotal within the details part we can see Signature Info
Im guessing this has something to do with the unique identifier for this strand
of malware.


Q3.

Determining the compilation timestamp of malware can reveal insights into its development and deployment timeline. What is the compilation timestamp of the malware that infected our network?
Super dumb 

2020-09-24 18:26

Q4 No point, need harder labs.


2020-10-15 02:47:37 UTC

Q4

Understanding when the broader cybersecurity community first identified the malware could help determine how long the malware might have been in the environment before detection. When was the malware first submitted to VirusTotal?

Q5

To completely eradicate the threat from Industries' systems, we need to identify all components dropped by the malware. What is the name of the .dat file that the malware dropped in the AppData folder?


I actually like this problem, this also assumes it didn't self replicate or store itself somewhere else

Key notes
- all components dropped by the malware.
-  .dat file  & AppData folder

I actually had to look this one up. Something called solarmarker.
Also found out there are three sandbox reports both static and dynamic that can be seen within virustotal.

Sadly they did not have the solarmarker.dat, which I am curious if the reports are credible or just missing the info

Q6.

It is crucial to identify the C2 servers with which the malware communicates to block its communication and prevent further data exfiltration. What is the C2 server that the malware is communicating with?

https://gogohid.com

The only url that can be found within memory. 

Need to move to harder rooms, these are fine for getting good at virus total and general research. 















Background: Implemented security through obscurity to my ubuntu server.

Local network  = 192.168.1.97

Honeytrap/Tarpit port = 22
Moved SSH port = ****

Most attempts on port 22 or the server was bot activity. (Almost all activity)

Most users or bots trying to bruteforce the login was met with a 
Implemented fail2ban to automatically clear out any bots or systems that was still trying to login and re trying the login attempt. 
Most attempts would time out and be banned after 5-10 minutes. 
Interesting to note that being open for a month or so, most system that hit the server were automated attacks. 
Typically the system giving up after being met with the initial tarpit. Once the system re tries the server login.

Interesting to note that of the few systems that would try to login:
Some would try to login after over some time until being banned

One instance where logins were tried port 22 from one ip, getting banned. More intensive system scan coming in from a different but similar ip. 
The attacker figure now trying port 4224, without successful login. 

Project is based on 
https://github.com/skeeto/endlessh
https://github.com/fail2ban/fail2ban

Endlessh install and setup notes. 
Setup and confirmed to be running by checking services and running nmap on a separate machine. 

nmap results are as follows:

SSH/port 22 looks like a legitimate port while port **** shows no to little information about it. 


SSH general notes
In order to obscure the Ubuntu server. I implemented netcat listers and other random listeners and services to obscure legitimate services.

Nmap results are as provided:

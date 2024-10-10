# Security Through Obscurity on Ubuntu Server

## Background

This project implements security through obscurity on my Ubuntu server to mitigate unauthorized access and potential attacks. 

### Network Configuration
- **Local Network:** `192.168.1.97`
- **Honeytrap/Tarpit Port:** `22`
- **Moved SSH Port:** `****` (specific port number omitted for security reasons)

## Observations

During the initial monitoring period, several interesting findings emerged:

- **Traffic Analysis:** 
  - Most attempts on port 22 (the default SSH port) were attributed to bot activity. 
  - Nearly all login attempts appeared to be automated, with significant brute-force attempts targeting the login credentials.

- **Intrusion Prevention:**
  - Implemented **Fail2Ban** to automatically block bots or systems that repeatedly attempted to log in.
  - Most of these attempts would timeout and result in a ban after 5-10 minutes of persistent failed logins.
  
- **Attack Patterns:**
  - After being exposed for about a month, it was noted that the majority of systems attempting to access the server were automated attacks. 
  - Most systems ceased attempts after encountering the initial tarpit, and subsequent retries were met with the implemented security measures.
  - Interestingly, some IP addresses persisted, trying to login intermittently until being banned.

- **Notable Incident:**
  - An instance was recorded where login attempts were made on port 22 from one IP address, resulting in a ban. Subsequently, a more intensive scan was initiated from a different but similar IP address, attempting access on port `4224`, which also did not succeed.

## Project Dependencies

This project is based on the following repositories:
- [Endlessh](https://github.com/skeeto/endlessh)
- [Fail2Ban](https://github.com/fail2ban/fail2ban)

### Installation and Setup Notes for Endlessh

1. **Installation:** Follow the installation instructions outlined in the Endlessh repository.
2. **Verification:** Confirm that Endlessh is running by checking the service status and using `nmap` from a separate machine.

### Nmap Results

When scanning the server, the results were as follows:

- **Port 22 (SSH):** Appeared to be a legitimate service.
- **Moved SSH Port (****):** Showed minimal information, reinforcing the obscurity strategy.

## Additional Security Measures

To further obscure the Ubuntu server, additional measures were implemented, including:
- Using **Netcat** listeners and random services to mask legitimate services.
  
### Conclusion

This security setup effectively deterred automated attacks while providing a robust environment for the Ubuntu server. Continuous monitoring and adjustments to the security protocols will help maintain system integrity.

## Future Considerations

- Further refine fail2ban rules for enhanced security.
- Implement additional logging and monitoring to analyze attack patterns over time.
- Consider deploying additional honeypots for deeper insights into attack methodologies.

---

*For further inquiries or to contribute to this project, please contact me at [your email address].*

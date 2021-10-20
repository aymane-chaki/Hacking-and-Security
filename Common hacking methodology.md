# Basic steps of hacking a system
1.  **<span style='color:orange'>Footprinting</span>** – the process of using passive methods of gaining information about the target system prior to performing the attack. The interaction with the target system is kept at minimum in order to avoid detection and alert the target about the attack. The footprinting can reveal vulnerabilities of the target system and improve the ease with which they can be exploited. Various methods are employed for footprinting, for example whois queries, Google searches, job boards search, network enumeration, operating system identification, etc.

2.  **<span style='color:orange'>Scanning</span>** – the process of taking information obtained from the footprinting phase in order to target the attack more precisely. Some of the metods used in this phase are port scans, ping sweeps, operating systems detection, observation of facilities used by the target, and so on.

3.  **<span style='color:orange'>Enumeration</span>** – the process of extracting more detailed information about the information obtained during the scanning phase to determine its usefulness. Some of the methods used in this step are user accounts enumeration, SNMP enumeration, UNIX/Linux enumeration, LDAP enumeration, NTP enumeration, SMTP enumeration, DNS enumeration, etc.

4.  **<span style='color:orange'>System hacking</span>** – the process of planning and executing the attack based on the information obtained in the previous phases. In this phase the attacker performs the actual hacking process using hacking tools.

5.  **<span style='color:orange'>Escalation of privilege</span>** – the process of obtaining privileges that are granted to higher privileged accounts than the attacker broke into originally. The goal of this step is to move from a low-level account (such as a guest account) all the way up to administrator.

6.  **<span style='color:orange'>Covering tracks</span>** – the process of removing any evidence of the attacker’s presence in a system. The attacker purges log files and removes other evidence needed for the owner of the system to determine that an attack occured.

7.  **<span style='color:orange'>Planting backdoors</span>** – the process of securing unauthorized remote access to a computer, so the attacker can access the system later without being detected. Backdoors are usually computer programs that give an attacker the remote access to a targeted computer system.
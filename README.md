My First TryHackMe CTF: Lessons from a Failed Attempt
I'm a cybersecurity student and IT admin, and I recently tried a TryHackMe Capture The Flag challenge targeting a Debian server at IP 10.10.128.185 running Apache 2.4.62. My goal was to gain a shell and escalate to root, but I didn't succeed. Still, I learned a lot about penetration testing and web vulnerabilities. Here's a simple summary of what I did, what I learned, and how I'll improve.
The Challenge
The CTF involved a web server with port 80 open, hosting an app in the /mbilling/ directory, likely MagnusBilling, a VoIP billing platform. Brute-forcing was not allowed, so I needed to find vulnerabilities to exploit. I used tools like Nmap, curl, gobuster, Burp Suite, Metasploit, Postman, and linPEAS. My plan was to scan the server, gain a shell, escalate to root, and find user.txt and root.txt flags.
Step 1: Reconnaissance - Mapping the Target
What I Did: I used Nmap to scan the target and found port 80 open with Apache 2.4.62. The http-enum script revealed a /robots.txt file, which pointed to /mbilling/. Using curl, I checked /robots.txt and saw it disallowed /mbilling/, suggesting sensitive content. I ran gobuster on /mbilling/ and found /index.html, /index.php, and /lib. Visiting /mbilling/index.php in my browser showed JavaScript variables like uploadFaxFilesize = 2M and moduleExtra = index.php/extra/read, hinting at MagnusBilling with a login page and possible file upload feature.
What I Learned: Nmap maps open ports and services, like a blueprint of the target. curl quickly grabs web content, revealing hidden paths. gobuster finds files and directories, such as /lib. Page source can show app details and vulnerabilities, like upload forms or endpoints.
Why It Failed: I didn't fully explore /lib or /index.php/extra/read. Next time, I'll use a larger wordlist with gobuster and check every path.
Step 2: Analyzing with Burp Suite
What I Did: I set up Burp Suite to intercept traffic to /mbilling/index.php. I started Burp, set my Firefox proxy to 127.0.0.1:8080, and enabled intercept. I tried three attacks: login bypass by testing admin:admin and SQL injection in Repeater; file upload, looking for a form hinted by uploadFaxFilesize; and command injection, testing ?cmd=whoami on /index.php and /index.php/extra/read.
What I Learned: Burp Suite is a hacker's tool for intercepting and modifying web requests. Login bypass with default credentials or SQL injection is common in CTFs. File uploads can be exploited by changing file types in Burp. Command injection tests parameters for direct command execution.
Why It Failed: I missed the upload form or used incorrect paths. Login bypass failed due to limited credential tests. Command injection didn't work because I didn't find the right parameter.
Step 3: Attempting a Shell
What I Did: I tried uploading a PHP shell via Burp, modifying file types. I tested command injection with curl on /mbilling/index.php?cmd=whoami. I searched Metasploit for PHP exploits.
What I Learned: Metasploit automates exploits, making shells easier. File uploads and command injection are key ways to gain access. Local File Inclusion can escalate to command execution by injecting code into logs.
Why It Failed: I didn't find the upload form or correct parameter. I should have used Postman for /index.php/extra/read and checked /lib/config.php for credentials.
Step 4: Planned Enumeration and Escalation
What I Planned: If I got a shell, I would have used linPEAS to find vulnerabilities, checked SUID binaries, writable files, and cron jobs, and escalated to root with SUID or a Metasploit kernel exploit.
What I Learned: linPEAS simplifies finding escalation paths like SUID binaries or cron jobs. Linux escalation exploits misconfigured files or binaries.
Why It Failed: No shell meant no enumeration or escalation. I need to focus on getting a foothold.
Key Takeaways

Recon is critical: Use Nmap, curl, and gobuster to map the target.
Burp Suite is essential: Master intercepting and testing requests.
Test everything: Try multiple credentials, parameters, and endpoints.
Persistence wins: Failure comes from not exploring enough paths.
Failure teaches: I now understand the hacking process better.

How I'll Improve

Use larger wordlists with gobuster.
Practice Burp Suite's Repeater and proxy setup.
Test API endpoints with Postman.
Learn reverse shell techniques with msfvenom and Netcat.
Study Linux escalation with linPEAS.

This CTF showed me the hacking process: scan, explore, exploit, escalate. I'm ready for the next challenge with more confidence!

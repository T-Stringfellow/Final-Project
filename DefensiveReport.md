# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology
_TODO: Fill out the information below._

The following machines were identified on the network:
- Target 1
  - **Linux Debian 3.16**:
  - **Apache Web Server (WordPress)**:
  - **192.168.1.110**:
- Name of VM 2
  - **Operating System**:
  - **Purpose**:
  - **IP Address**:
- Etc.

### Description of Targets
_TODO: Answer the questions below._

The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

####HTTP Request Size Monitor
_TODO: Replace `Alert 1` with the name of the alert._

HTTP Request Size Monitor is implemented as follows:

`WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute`

  - **Metric**: HTTP Requests per Minute
  - **Threshold**: 3500 bytes per minutes
  - **Vulnerability Mitigated**: DoS // Brute Force // Code Injection 
  - **Reliability**: MEDIUM: This alert will flag any malicious HHTP attempt but poses a strong potential for false positives. Any large amount of HTTP traffic will result in an error even if the traffic is non-malicious. Human review will ensure filtering of benign alerts.

#### Excessive HTTP Errors
Excessive HTTP Errors is implemented as follows:

`WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes`

  - **Metric**: HTTP Response Code
  - **Threshold**: Response code is above '400' (server-client 'error' code range)
  - **Vulnerability Mitigated**: Enumeration // Brute Force
  - **Reliability**: HIGH: The alert is highly reliable as measuring by error codes 400 and above will filter out any normal or successful responses. Further isolating high frequencies of error code responses will help highlight a potential attack.

#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:

`WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes`

  - **Metric**: CPU Usage % 
  - **Threshold**: Over 0.5
  - **Vulnerability Mitigated**: Malicious software/programs (I.e. malware or virus programs) consuming host resources.
  - **Reliability**: HIGH: This alert is guaranteed to enumerate malicious software, additionally any non-malware based excessive CPU consumption has logistical implications and should be addressed in all circumstances. 

_TODO Note: Explain at least 3 alerts. Add more if time allows._

### Suggestions for Going Further (Optional)
_TODO_: 
- Each alert above pertains to a specific vulnerability/exploit. Recall that alerts only detect malicious behavior, but do not stop it. For each vulnerability/exploit identified by the alerts above, suggest a patch. E.g., implementing a blocklist is an effective tactic against brute-force attacks. It is not necessary to explain _how_ to implement each patch.

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Vulnerability 1
  - **Patch**: TODO: E.g., _install `special-security-package` with `apt-get`_
  - **Why It Works**: TODO: E.g., _`special-security-package` scans the system for viruses every day_
**Excessive HTTP Errors**
- Patch: WordPress Hardening
    - Implement regular updates to WordPress 
        - WordPress Core 
        - PHP version
        - Plugins
    - Install security plugin(s)
        - i.e. Wordfence (adds security functionality)
    - Disable unused WordPress features and settings like:
        - WordPress XML-RPC (on by default)
        - WordPress REST API (on by default)
    - Block requests to /?author=<number> by configuring web server settings
    - Remove WordPress logins from being publicly accessible specifically:
        - /wp-admin 
        - /wp-login.php
- Why It Works: 
    - Regular updates to WordPress, the PHP version and plugins is an easy way to implement patches or fixes to exploits/vulnerabilities.
    - Depending on the WordPress security plugin it can provide things like:
        - Malware scans
        - Firewall
        - IP options (to monitor/block suspicious traffic)
    - REST API is used by WPScan to enumerate users
        - Disabling it will help mitigate WPScan or enumeration in general
    - XML-RPC uses HTTP as it’s method of data transport
    - WordPress links (permalinks) can include authors (users)
        - Blocking request to view the all authors (users) helps mitigate against user enumeration attacks
    - Removal of public access to WordPress login helps reduce the attack surface
**CPU Usage Monitor**
- Patch: Virus or Malware hardening
    - Add or update to a good antivirus.
- Why It Works: 
    - Antivirus programs specialize in removal, detection and overall prevention of malicious threats.
        - Most modern antivirus typically include an active scan and networking monitoring solutions, allowing for real-time threat mitigation.
   

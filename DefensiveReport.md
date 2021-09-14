# Blue Team: Summary of Operations

## Table of Contents
- [Network Topology](#Network-Topology)
- [Description of Targets](#Description-of-Targets)
- [Monitoring the Targets](#Monitoring-the-Targets)
- [Suggestions for Going Further](#Suggestions-for-Going-Further)

## Network Topology

The following machines were identified on the network:
- Target 1
  - **Linux Debian 3.16**:
  - **Apache Web Server (WordPress)**:
  - **192.168.1.110**:

<img src="/Images/net-topology.png">

## Description of Targets

The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

## Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

###    1. HTTP Request Size Monitor

HTTP Request Size Monitor is implemented as follows:

`WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute`

  - **Metric**: HTTP Requests per Minute
  - **Threshold**: 3500 bytes per minutes
  - **Vulnerability Mitigated**: DoS // Brute Force // Code Injection
  - **Reliability**: MEDIUM: This alert will flag any malicious HHTP attempt but poses a strong potential for false positives. Any large amount of HTTP traffic will result in an error even if the traffic is non-malicious. Human review will ensure filtering of benign alerts.

### Excessive HTTP Errors
Excessive HTTP Errors is implemented as follows:

`WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes`

  - **Metric**: HTTP Response Code
  - **Threshold**: Response code is above '400' (server-client 'error' code range)
  - **Vulnerability Mitigated**: Enumeration // Brute Force
  - **Reliability**: HIGH: The alert is highly reliable as measuring by error codes 400 and above will filter out any normal or successful responses. Further isolating high frequencies of error code responses will help highlight a potential attack.

### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:

`WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes`

  - **Metric**: CPU Usage %
  - **Threshold**: Over 0.5
  - **Vulnerability Mitigated**: Malicious software/programs (I.e. malware or virus programs) consuming host resources.
  - **Reliability**: HIGH: This alert is guaranteed to enumerate malicious software, additionally any non-malware based excessive CPU consumption has logistical implications and should be addressed in all circumstances.


## Suggestions for Going Further

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:


### 1. **Excessive HTTP Errors**
- **Patch:** WordPress // User Credential Hardening.
    - A robust password policy (special characters, numbers, expiration dates) should be implemented to reduce the likelihood of a successful brute force attack. Additionally public key authentication can also be implemented.
- **Why it Works:**
    - A strong password greatly reduces the chances of a successful attack. Public key authentication renders brute force attacks almost impossible.

### 2. **HTTP REQUEST SIZE**
- **Patch**: Implement and Intrusion Detection System (IDS), or preferably, Intrusion Prevention System (IPS) solution.
- **Why It Works**: An IDS will monitor the network traffic for signs of an attempted DDoS attack, or attempted delivery of a malicious payload. While the IDS will still require human intervention it is an extremely robust front-line alert system. An IPS, when baselined and configured properly may be able to effectively thwart DDoS attacks without the need for human intervention.

### 3. **CPU Usage Monitor**
- **Patch:** Virus or Malware hardening
    - Add or update to a proven antivirus solution.
- **Why It Works:**
    - Antivirus programs specialize in removal, detection, and overall prevention of malicious threats.
    - Most commercially available antivirus programs typically include an active scan and networking monitoring solutions, allowing for real-time threat mitigation.

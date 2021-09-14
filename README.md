# Final-Project
Final Vulnerability Assessment for CyberSecurity Bootcamp
 

## [DEFENSIVE REPORT](https://github.com/T-Stringfellow/Final-Project/blob/51df6221e2ff9a8d8cd3fb4138b7387bc807ec09/DefensiveReport.md)
- The initial phase of the project was to develop a series of alerts using *Kibana* to help defend a vulnerable WordPress Server instance. Specifics regarding the nature and thresholds of the respective alerts are detailed in the documentation. Further, additional mitigation strategies are outlined towards the end of the report.

## [OFFENSIVE REPORT](https://github.com/T-Stringfellow/Final-Project/blob/51df6221e2ff9a8d8cd3fb4138b7387bc807ec09/OffensiveReport.md) 
- The vulnerable WordPress server was scanned and exploited to leverage access to the entire file system, allowing for exfiltration of sensitive data. Ultimately weaknesses in the system were utilized to gain *root* access via an SSH shell.

## [NETWORK REPORT](https://github.com/T-Stringfellow/Final-Project/blob/51df6221e2ff9a8d8cd3fb4138b7387bc807ec09/NetworkReport.md) 
- During this phase of the project *WireShark* was used to review network traffic. Several objectives were in place ranging from analyzing normal behavior to isolating and documenting illegal network activity

## [RED TEAM OPERATIONS - GROUP PRESENTATION](Link) 
 - While the entirety of the reporting and exploitation arms of the project were completed entirely independently by myself, the final presentation was a collaborative effort to document and share findings with other members of my cohort.
         - This Red Team presentation delves into specific techniques and exploits leveraged to gain access to the vulnerable system. Analysis of the techniques used compared to configured alerts is also provided with detail on detection avoidance strategies.

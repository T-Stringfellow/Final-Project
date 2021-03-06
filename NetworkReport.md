# Network Forensic Analysis Report

## Table of Contents
- [Time Thieves](#Time-Thieves)
- [Vulnerable Windows Machine](#Vulnerable-Windows-Machine)
- [Illegal Downloads](#Illegal-Downloads)


## <ins>Time Thieves</ins> 
You must inspect your traffic capture to answer the following questions:

1. **What is the domain name of the users' custom site?**
	- The domain name is <ins>**frank-n-ted-dc.frank-n-ted.com**</ins>
		- Filter: `ip.addr==10.6.12.0/24`
	
	- Screenshot:
	<img src="/Images/WireS/FnT-DC.png">

2. What is the IP address of the Domain Controller (DC) of the AD network?
	- The IP address is **`10.6.12.12`**
	
3. What is the name of the malware downloaded to the 10.6.12.203 machine?
	- Downloaded file **`june11.dll`**
		- *Filter applied:* `ipaddr==10.16.12.203 and http.request.method==GET`

	Screenshot:
	<img src="/Images/WireS/june11.png">
   
4. What kind of malware is this classified as?
	- This type of malware is flagged as a **TROJAN**.
		- VirusTotal Analysis:
	<img src="/Images/WireS/virustotal.png">

---

## <ins>Vulnerable Windows Machine</ins>

1. Find the following information about the infected Windows machine:
    - Host name: **<ins>ROTTERDAM-PC</ins>**
    - IP address **<ins>172.16.4.205</ins>**
    - MAC address **<ins>00:59:07:b0:63:a4</ins>**

    
2. What is the username of the Windows user whose computer is infected?
	- Windows user is <ins>**matthijs.devries**</ins>
	
	- *`kerberos.CNameString`* applied as column filter to trace user name
	- Screenshot:
	<img src="/Images/WireS/matth.png">

3. What are the IP addresses used in the actual infection traffic?
	- The infected host machine **`ROTTERDAM-PC (172.16.4.205)`** was found to have an abnormal large amount of packet transfer with outside IP **`185.243.115.84`** suggesting this latter IP as an infection traffic avenue. 
		- Following the conversation statistics from **`172.16.4.205`** revealed the **`185.243.115.84`** IP as a warranting further investigation.

	- Screenshot
	<img src="/Images/WireS/conversation.png">
 
4. As a bonus, retrieve the desktop background of the Windows host.

	- Stream between `172.16.4.205` and `185.243.115.8`4 found POST exchanges without solicited GET request revealed the following desktop image sent to `185.243.115.84`
		- <ins>DESKTOP IMAGE:</ins>
	<img src="/Images/WireS/desktop.png">

---

## <ins>Illegal Downloads</ins>

1. Find the following information about the machine with IP address `10.0.0.201`:
    - MAC address: **<ins>00:16:17:18:66:c8</ins>**
    - Windows username: **<ins>elmer.blanco</ins>**
    - OS version: **<ins>Windows NT 10.0 x64</ins>**

		- Filter Used: `ip.addr==10.0.0.201 and kerberos.CNameString`

	- Screenshot:
	<img src="/Images/WireS/elmer.png">

2. Which torrent file did the user download?

	- Torrent file was **<ins>Betty_Boop_Rthythm_on_the_Reservation.avi.torrent</ins>**

		- Filter used: `ip.addr=10.0.0.201 and http.request.method==GET`
 	- Screenshot:
	<img src="/Images/WireS/torrent.png">

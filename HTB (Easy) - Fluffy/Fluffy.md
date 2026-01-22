https://github.com/yeyingsrc/Hackthebox-Walkthrough/blob/7314c9eea3cbd1b907c81613800e3a4f698bf0e2/Easy/Fluffy/Walkthrough.md
As is common in real life Windows pentests, you will start the Fluffy box with credentials for the following account: j.fleischman / J0elTHEM4n1990!
# Nmap
tcp
![](Pasted%20image%2020250808101615.png)
udp
# SMB
cek domain
![](Pasted%20image%2020250808101953.png)
cek login
![](Pasted%20image%2020250808151856.png)
cek share folder
![](Pasted%20image%2020250808151928.png)
Cek permission, folder IT can "WRITE", it mean we can uploaded something

![](Pasted%20image%2020250815151358.png)

IT folder is interesting, explore it
![](Pasted%20image%2020250808152313.png)
Open the pdf file, there is known vulnerability
![](Pasted%20image%2020250815132104.png)
CVE-2025-24996 Critical -> nope, we dont use web
CVE-2025-24071 Critical -> yes
CVE-2025-46785 High -> no PoC
CVE-2025-29968 High -> no PoC
CVE-2025-21193 Medium -> no PoC
CVE-2025-3445 Low -> no PoC

we found PoC
![](Pasted%20image%2020250815160109.png)

run the exploit
![](Pasted%20image%2020250815162400.png)

Upload malicious file
![](Pasted%20image%2020250815162246.png)
# 1. Target

| Field             | IP Ranger       |
| ----------------- | --------------- |
| VPN IP Range      | 10.10.200.0/24  |
| External IP Range | 192.168.80.0/24 |
| Internal IP Range | 192.168.98.0/24 |
# 2. Active Host
## a. Internal
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311114755.png)
192.168.80.1
192.168.80.10
## b. External
Nope
# 3. Active Port

| IP            | TCP   | UDP    |
| ------------- | ----- | ------ |
| 192.168.80.1  | 53,80 | 53,123 |
| 192.168.80.10 | 22    | -      |
## a. 192.168.80.1
### TCP
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311124703.png)
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311132434.png)
### UDP
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250312092749.png)
## b. 192.168.80.10
### TCP
![](Pasted%20image%2020250312131313.png)
![](Pasted%20image%2020250312132013.png)
### UDP
Nope
# 4. Web - 192.168.80.1
## a. User Interface
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311134804.png)
Modul:
- Login
## b. Direktori
a. feroxbuster -> Nope
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311142835.png)
b. dirsearch -> Nope
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311142929.png)
## c. Subdomain
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250311150625.png)
Nope
# 5. NTP
   ## a. nmap
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250312102843.png)
# 6. DNS
nslookup
![](../Hackthebox/Lab/Easy/HTB%20(Easy)%20-%20Dog/Pasted%20image%2020250312113013.png)
# 7. Web - 192.168.80.10
## a. User Interface
![](Pasted%20image%2020250312132403.png)
## b. Direktori listing
192.168.80.10:9191
![](Pasted%20image%2020250312133008.png)
## c. Newsletter
reflected
![](Pasted%20image%2020250312133911.png)
ada command injection
![](Pasted%20image%2020250312134234.png)
coba reverse shell -> dapat reverse shell
![](Pasted%20image%2020250312135103.png)
![](Pasted%20image%2020250312135125.png)
cat /etc/passwd ada comment
get privilege credential Admin@962
![](Pasted%20image%2020250312140157.png)
get shell
![](Pasted%20image%2020250312140342.png)
# 8. Shell - 192.168.80.10
sudo -L
![](Pasted%20image%2020250312141035.png)
ada ALL : ALL) ALL -> kemungkina bisa root
ifconfig -> ada another interface -> local network
![](Pasted%20image%2020250312141501.png)
ada db sqlite in mozilla
![](Pasted%20image%2020250314134625.png)
ada credential pada url 192.168.98.30
john@child.warfare.corp
User1@#$%6
![](Pasted%20image%2020250314134858.png)
# 9. Pivoting
connect ligolo
![](Pasted%20image%2020250313093101.png)
saat nmap -sn malah open semuanya
coba fpin
![](Pasted%20image%2020250317125747.png)
# 10. SMB
192.168.98.2 -> dc1.warfare.corp
192.168.98.30 -> child.warfare.corp
192.168.98.120 -> cdc.child.warfare.corp

From live ip we try to bruteforce login
![](Pasted%20image%2020250317134354.png)
nmap 192.168.98.30
![](Pasted%20image%2020250317135725.png)
![](Pasted%20image%2020250317145004.png)
corpmngr:User4&*&*
![](Pasted%20image%2020250317144929.png)
bruteforce credential tsb dengan ip
berhasil login ke 192.168.98.120
![](Pasted%20image%2020250317150210.png)
![](Pasted%20image%2020250318091137.png)
# 11. Winrm
![](Pasted%20image%2020250317141008.png)
masuk
![](Pasted%20image%2020250317141241.png)
cek User
![](Pasted%20image%2020250317142131.png)
# 12. Bloodhound
![](Pasted%20image%2020250318142248.png)
 cek user
 ![](Pasted%20image%2020250318145048.png)

# 13. Secretdump
krbtgt -> ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2
![](Pasted%20image%2020250319095713.png) try for all account
![](Pasted%20image%2020250319102115.png)
enumerate SID using lookupsid
![](Pasted%20image%2020250319105330.png)
![](Pasted%20image%2020250319105709.png)
child.warfare.corp -> Domain SID is: S-1-5-21-3754860944-83624914-1883974761
warfare.corp -> Domain SID is: S-1-5-21-3375883379-808943238-3239386119
# 14 Privilege Escalation
make golden ticket
```

```



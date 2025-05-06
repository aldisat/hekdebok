![](../HTB%20Medium%20-%20TheFrizz/Scepter.png)
# A.  Service Enumeration

```
10.10.11.65
```

| IP  | Port                                                            |
| --- | --------------------------------------------------------------- |
| TCP | 53,88,111,135,139,389,445,464,593,636,2049,3268,3269,5985,5986  |
| UDP | -                                                               |
## 1. TCP Active Port
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250423141358.png)
## 2. UDP Active Port
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425093018.png)
## 3. Domain
```
enum4linux-ng -A 10.10.11.65
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425133419.png)
```
LDAP -> scepter.htb0
DNS -> dc01.scepter.htb
```
generate bloodhound
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428052444.png) 
# B. Initial Access
## 1.  SMB
Recon nmap
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425094848.png)
## 2. LDAP
Reco.  nmap
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425100351.png)
login without credential -> success but empty
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425102736.png)
## 3. NFS
cek share folder
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425103920.png)
access /helpdesk form local machine
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425105115.png)
show file, and we can see a couple public and private and they named as baker, also 3 pfx files.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250425105235.png)
Lets generate pfx file for baker using openssl, but they ask for password. lets extract password from one of the pfx file.
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250427210407.png)
we got password -> newpassword
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250427210552.png)
use the password to generate baker.pfx file
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250427210911.png)
get hash with certipy-ad
```
aad3b435b51404eeaad3b435b51404ee:18b5fb0d99e7a475316213c15b6f22ce
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428051536.png)
test login LDAP with that hash -> success
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428055027.png)
## 4. Bloodhound
generate bloodhouond file
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428055125.png)
the bloodhound relation
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428105724.png)

force change password for account a.carter
```
P@ssw0rd123
```
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428105608.png)
test new a.carter credential
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428125756.png)
## Certificate
A.carter is member of IT Support Group, and IT Support Group is member of Staff Access Certificate
![](../HTB%20Medium%20-%20TheFrizz/Pasted%20image%2020250428133338.png)

# C. Privilege Escalation
## 1. ..
# D. Post Exploitation
## 1. User Flag
## 2. Root Flag

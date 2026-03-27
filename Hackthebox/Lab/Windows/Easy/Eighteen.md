# Port

![](../../../../attachments/Pasted%20image%2020260204144807.png)

Version
![](../../../../attachments/Pasted%20image%2020260205132849.png)

# Reconnaisance
## Web
Front
![](../../../../attachments/Pasted%20image%2020260204151721.png)

dirsearch
![](../../../../attachments/Pasted%20image%2020260204152649.png)

subdomain -> NOPE
![](../../../../attachments/Pasted%20image%2020260204161400.png)

no injection
![](../../../../attachments/Pasted%20image%2020260204172511.png)

## MSSQL
login use credential -> success
![](../../../../attachments/Pasted%20image%2020260205154109.png)

list db, kevin can not access db financial_planner
![](../../../../attachments/Pasted%20image%2020260205163511.png)

list user
![](../../../../attachments/Pasted%20image%2020260205162958.png)

can not login as sa, but can login as appdev
![](../../../../attachments/Pasted%20image%2020260205162903.png)

open financial_planner db 
![](../../../../attachments/Pasted%20image%2020260205164203.png)

get admin credential
```
admin

admin@eighteen.htb

pbkdf2:sha256:600000$AMtzteQIG7yAbZIa$0673ad90a0b4afb19d662336f0fce3a9edd0b7b19193717be28ce4d66c887133
```

encrypt
![](../../../../attachments/Pasted%20image%2020260206155203.png)

try login using that credential and success
![](../../../../attachments/Pasted%20image%2020260207135039.png)

there are info about application -> flask financial planner 1 -> no CVE
![](../../../../attachments/Pasted%20image%2020260207135242.png)

Try enum user using nxc
![](../../../../attachments/Pasted%20image%2020260209152144.png)

try update and upgrade the kali linux, and it work again
![](../../../../attachments/Pasted%20image%2020260209202535.png)

we get the users
```
jamie.dunn
jane.smith
alice.jones
adam.scott
bob.brown
carol.white
dave.green
```

## WSMAN
success login but can not dir
![](../../../../attachments/Pasted%20image%2020260205153455.png)

brute users.txt with password iloveyou1
![](../../../../attachments/Pasted%20image%2020260209204500.png)

get user.txt
![](../../../../attachments/Pasted%20image%2020260209204905.png)

# Priviledge Escalation
whoami /all, adam.scott in IT group
![](../../../../attachments/Pasted%20image%2020260210123346.png)

get more info
![](../../../../attachments/Pasted%20image%2020260210124432.png)

Check WIndows Server Version
![](../../../../attachments/Pasted%20image%2020260210141514.png)

Check for CVE -> Bad Successor
![](../../../../attachments/Pasted%20image%2020260210141936.png)

git clone this 
![](../../../../attachments/Pasted%20image%2020260210145815.png)

send BadSuccessor.ps1 to windows server
![](../../../../attachments/Pasted%20image%2020260210150640.png)

create account satria
![](../../../../attachments/Pasted%20image%2020260210152202.png)
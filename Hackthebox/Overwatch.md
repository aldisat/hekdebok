Start => 13.25 27 Februari 2026
Finish =>

Note
```
Get Sid => S-1-5-21-2797066498-1365161904-233915892
terdapat port internal yang aktif => http://overwatch.htb:8000/MonitorService

dnsHostName: S200401.overwatch.htb #ini hostname
domainFunctionality: 7
Windows Server 2022 Build 20348 x64

Server=localhost;
Database=SecurityLogs;
User Id=sqlsvc;
Password=TI0LKcfHzZw1Vv;

Login SMB -> success
Login WinRM -> failed
Login LDAP -> failed
Login MSSQL ->

```

# I. Initial Access
## A. nmap tcp?
![](../../../../attachments/Pasted%20image%2020260227132911.png)

### nmap tcp version?
![](../../../../attachments/Pasted%20image%2020260227133416.png)

## B. nmap udp?
![](../../../../attachments/Pasted%20image%2020260227133913.png)

### nmap udp version?
![](../../../../attachments/Pasted%20image%2020260227134157.png)

## C. set /etc/host
![](../../../../attachments/Pasted%20image%2020260227133842.png)
# II. Service Enumeration

## A. SMB
### 1. Version? SMB v2
![](../../../../attachments/Pasted%20image%2020260227144842.png)

### 2. nmap enum? nothing
![](../../../../attachments/Pasted%20image%2020260227135705.png)

### 3. enum4linux?
![](../../../../attachments/Pasted%20image%2020260227141321.png)
Get Sid => S-1-5-21-2797066498-1365161904-233915892
![](../../../../attachments/Pasted%20image%2020260227141351.png)

### 4. null session? success
![](../../../../attachments/Pasted%20image%2020260227140429.png)
Check folder one by one, only software
![](../../../../attachments/Pasted%20image%2020260227141909.png)
download all
![](../../../../attachments/Pasted%20image%2020260227143137.png)
terdapat port internal yang aktif => http://overwatch.htb:8000/MonitorService
![](../../../../attachments/Pasted%20image%2020260227144017.png)
Recon riduser => error
![](../../../../attachments/Pasted%20image%2020260227210015.png)

### 5. coba cek lagi dengan smbmap (sorry ngacak)
![](../../../../attachments/Pasted%20image%2020260228212054.png)

## B. LDAP
### 1. nmap scan dulu => yang penting-penting
```
dnsHostName: S200401.overwatch.htb #ini hostname
domainFunctionality: 7 

```

![](../../../../attachments/Pasted%20image%2020260301071430.png)

### 2. cek anonymous bind => diblok
![](../../../../attachments/Pasted%20image%2020260301200525.png)

### 3. Try rid-brute => berhasil dapat user
![](../../../../attachments/Pasted%20image%2020260301202828.png)

### 4. try AS-REP-Roast => not succes
![](../../../../attachments/Pasted%20image%2020260302105254.png)

## C. .NET file
### 1. Try read exe file that we found in SMB using monodis
vim overwatch,exe it tells use .NET
![](../../../../attachments/Pasted%20image%2020260302181536.png)

### 2. search for sensitive string in file, and we found password
```
L_0029:  ldstr "Server=localhost;Database=SecurityLogs;User Id=sqlsvc;Password=TI0LKcfHzZw1Vv;"
```
![](../../../../attachments/Pasted%20image%2020260303205010.png)

## D. SMB After Get Creddential
### 1. successfully login SMB
![](../../../../attachments/Pasted%20image%2020260304054722.png)

### 2. cek shares satu2, hanya share SYSVOL yang ada isinya
![](../../../../attachments/Pasted%20image%2020260304205514.png)

![](../../../../attachments/Pasted%20image%2020260306203850.png)
## E. LDAP After Get Credentials
### 1. login ldap -> failed
## F. MSSQL
### 1. try nmap one more time, is any post that i missed
![](../../../../attachments/Pasted%20image%2020260307122344.png)
### 2. yes there is mssql port, try login using that credential, failed wkwk
![](../../../../attachments/Pasted%20image%2020260307122605.png)

### 3. try enum using nmap
![](../../../../attachments/Pasted%20image%2020260307123232.png)

### 4. cek login -> failed
![](../../../../attachments/Pasted%20image%2020260307123537.png)

### 5. Cek login with another tools -> succed ✅
![](../../../../attachments/Pasted%20image%2020260307125052.png)
### 6. Login to the service => succed ✅
![](../../../../attachments/Pasted%20image%2020260307125314.png)

### 7. enum db overwatch => empty
![](../../../../attachments/Pasted%20image%2020260307135039.png)
check linked server -> SQL07
![](../../../../attachments/Pasted%20image%2020260307144928.png)
# III. Priviledge Escalation
# IV. Post Exploitation

# I. Enumeration
## 1. TCP
![](../../../../attachments/Pasted%20image%2020260423144107.png)
## 2. UDP
![](../../../../attachments/Pasted%20image%2020260423145026.png)

# II. Initial Access
## 1. Port 80
### a. wingdata.htb
![](../../../../attachments/Pasted%20image%2020260423143852.png)
#### -- subdomain -> cant filter fs
![](../../../../attachments/Pasted%20image%2020260423144525.png)
### b. ftp.wingdata.htb
![](../../../../attachments/Pasted%20image%2020260423143945.png)
### c. CVE
#### -- Check searchsploit
![](../../../../attachments/Pasted%20image%2020260423144203.png)
#### -- ada ternyata
![](../../../../attachments/Pasted%20image%2020260423145123.png)
#### -- google
![](../../../../attachments/Pasted%20image%2020260423144936.png)
### d. Get Shell
![](../../../../attachments/Pasted%20image%2020260423145337.png)
#### -- passwd
![](../../../../attachments/Pasted%20image%2020260423145433.png)
#### -- make interactive -> session expired
![](../../../../attachments/Pasted%20image%2020260423150817.png)
#### -- home kosong
![](../../../../attachments/Pasted%20image%2020260423151706.png)
#### -- ls -lha
![](../../../../attachments/Pasted%20image%2020260423193439.png)
#### -- ssh key
![](../../../../attachments/Pasted%20image%2020260423193532.png)

#### -- local port
![](../../../../attachments/Pasted%20image%2020260423200850.png)
#### -- get proper shell
![](../../../../attachments/Pasted%20image%2020260424094237.png)
![](../../../../attachments/Pasted%20image%2020260424094212.png)
### e. Get Credential
#### -- Get password
![](../../../../attachments/Pasted%20image%2020260424102205.png)
#### -- identifie hash type
![](../../../../attachments/Pasted%20image%2020260424102410.png)
#### -- exhausted
![](../../../../attachments/Pasted%20image%2020260424132000.png)

#### -- check the format hash
![](../../../../attachments/Pasted%20image%2020260424132339.png)
#### -- successfuly get password
```
!#7Blushing^*Bride5
```
![](../../../../attachments/Pasted%20image%2020260424132445.png)
#### -- access shell
![](../../../../attachments/Pasted%20image%2020260424132604.png)

# III. Privilege Escalation
## 1. sudo -l
![](../../../../attachments/Pasted%20image%2020260424132727.png)
## 2. Local
![](../../../../attachments/Pasted%20image%2020260424132855.png)
## 3. Interesting Backup function
![](../../../../attachments/Pasted%20image%2020260424133129.png)
### a. test running
![](../../../../attachments/Pasted%20image%2020260424134148.png)
### b. find for backup file -> nothing
![](../../../../attachments/Pasted%20image%2020260424140045.png)
## 4. check /opt
![](../../../../attachments/Pasted%20image%2020260428084826.png)
## 5. Python version
![](../../../../attachments/Pasted%20image%2020260428093233.png)
### a. CVE
![](../../../../attachments/Pasted%20image%2020260428111315.png)
### b. PoC
https://github.com/AzureADTrent/CVE-2025-4517-POC
![](../../../../attachments/Pasted%20image%2020260428111710.png)
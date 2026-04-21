```

```
# I. Initial Access
tcp
![](../../../../attachments/Pasted%20image%2020260417124019.png)

udp and tcp version
![](../../../../attachments/Pasted%20image%2020260417135737.png)
# II.  Service Enumeration
## A. Port 80 (User.txt)
### 1. Input double quote and single quote -> no error and no reflected
![](../../../../attachments/Pasted%20image%2020260417124333.png)

### 2. dirsearch -> no sensitif data
![](../../../../attachments/Pasted%20image%2020260417135616.png)

### 3. ffuf -> no subdomain
![](../../../../attachments/Pasted%20image%2020260417141350.png)

### 4. nikto -> admin login
![](../../../../attachments/Pasted%20image%2020260417153708.png)

#### a. success register and login -> Cameleon CMS 2.9.0
![](../../../../attachments/Pasted%20image%2020260417154143.png)

#### b. Cek CVE
![](../../../../attachments/Pasted%20image%2020260417155544.png)

##### -- Try the CVE, change
![](../../../../attachments/Pasted%20image%2020260417155938.png)

##### -- success huhu.jiji to admin
![](../../../../attachments/Pasted%20image%2020260417160659.png)

##### -- get LFI, there user willian and trivia
![](../../../../attachments/Pasted%20image%2020260417161227.png)
##### -- Get user.txt, from /home/william/user.txt
![](../../../../attachments/Pasted%20image%2020260417161834.png)
## B. Port 54321
redirect to facts.htb:9001
![](../../../../attachments/Pasted%20image%2020260417142814.png)

dirsearch -> no sensitif endpoint
![](../../../../attachments/Pasted%20image%2020260417143436.png)

subdomain -> none
![](../../../../attachments/Pasted%20image%2020260417143750.png)
# III. Privilege Escalation
## A. LFI to Shell
based on this
https://medium.com/@sujeetkamblesrk/from-lfi-to-rce-how-i-turned-a-file-read-into-shell-access-073ec2e5501e 
list conf file in linux
![](../../../../attachments/Pasted%20image%2020260420101652.png)

turbo intruder for 200 response, after review response no sensitive credential leak
![](../../../../attachments/Pasted%20image%2020260420102215.png)

## B. Change password Admin for check configuration
![](../../../../attachments/Pasted%20image%2020260420105404.png)
### 1. Get AWS credential
![](../../../../attachments/Pasted%20image%2020260420105612.png)
```
AKIAD043010CF0278E0C
w82zF756iY1D/Jr7V2sMQkjIbnLM0EvpjovKRmqH
randomfacts
us-east-1
http://localhost:54321
http://facts.htb/randomfacts
```

### 2. Set credentials
![](../../../../attachments/Pasted%20image%2020260420140122.png)

### 3. Login and check endpoint, internal is interesting
![](../../../../attachments/Pasted%20image%2020260420140242.png)

### 4. list all files for each endpoints
![](../../../../attachments/Pasted%20image%2020260420140952.png)

### 5. Download all file, include .ssh
![](../../../../attachments/Pasted%20image%2020260420143232.png)
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJbGDKHzQgbvq7NBrPD5VR+ep+ihfs5Wl1t+1Enrb0o5 

-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABC0yngumF
iucJJYX2qVxawQAAAAGAAAAAEAAAAzAAAAC3NzaC1lZDI1NTE5AAAAIJbGDKHzQgbvq7NB
rPD5VR+ep+ihfs5Wl1t+1Enrb0o5AAAAoAMiAt/9bQOtLZIOVVJZiXvtFK4OqG97HsbZxJ
p6XVABBvvb2sWPjcL64ZJ6AmLOI6gRkOFMvGyIvhv+e81x7D3vZlWyZ+aI2jLBuS56c9EU
H8jvFunAcb+mAu3GsOCdqnkLmJxMfAz5rk1i64IBE2mw2aRNg1GGLZCUb2D52UW+saN4a8
Q7tGJr8DA2i5sUfWYPqORBSVIJ22k1oEtrnik=
-----END OPENSSH PRIVATE KEY-----

```
### 6. extract privatekey
using ssh2john
![](../../../../attachments/Pasted%20image%2020260420144131.png)
get the password -> `dragonballz`
![](../../../../attachments/Pasted%20image%2020260420145802.png)

## C. Get Shell
![](../../../../attachments/Pasted%20image%2020260420150047.png)
### 1. Check sudo -l
facter bisa di ekseskusi tanpa 
![](../../../../attachments/Pasted%20image%2020260420150341.png)
version
![](../../../../attachments/Pasted%20image%2020260420151212.png)
facter function
![](../../../../attachments/Pasted%20image%2020260420152305.png)
### 2. Check Local Port
![](../../../../attachments/Pasted%20image%2020260421095638.png)
### 3. Check gtfobins
![](../../../../attachments/Pasted%20image%2020260421101941.png)
#### -- implement the method form gtfobins
facter can run rb script, try to find rubi script for run shell
![](../../../../attachments/Pasted%20image%2020260421103101.png)
-- example facter 
![](../../../../attachments/Pasted%20image%2020260421122539.png)
```
Facter.add(:pwn) do
  setcode { exec("/bin/bash -p") }
end

```
#### -- get root
![](../../../../attachments/Pasted%20image%2020260421124707.png)
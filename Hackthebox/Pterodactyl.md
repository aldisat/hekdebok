# I. Initial Access
## 1. TCP Port
![](../../../../attachments/Pasted%20image%2020260421132209.png)
## 2. UDP Port
# II. Service Enumeration
## 1. Port 80
![](../../../../attachments/Pasted%20image%2020260421133907.png)
### a. dirsearch
![](../../../../attachments/Pasted%20image%2020260421134654.png)
### b.subdomain
![](../../../../attachments/Pasted%20image%2020260421134542.png)
#### -- coba lagi dengan fs 145 => terdapat domain panel
![](../../../../attachments/Pasted%20image%2020260422103503.png)
### c. Version
pterodactyl panel v1.11.10
![](../../../../attachments/Pasted%20image%2020260421134947.png)
#### -- try vind cve for pterodactyl
![](../../../../attachments/Pasted%20image%2020260422090708.png)
#### -- not vulnerable :(
![](../../../../attachments/Pasted%20image%2020260422091113.png)
#### -- try again with ip, its vulnerable ![](../../../../attachments/Pasted%20image%2020260422094150.png)
### d. Try CVE with new subdomain panel
![](../../../../attachments/Pasted%20image%2020260422103712.png)
#### -- Yes success
![](../../../../attachments/Pasted%20image%2020260422105452.png)
```
pterodactyl:PteraPanel@127.0.0.1:3306/panel                                
```
#### -- phpggc tidak terinstall
![](../../../../attachments/Pasted%20image%2020260422110008.png)
#### -- Dirsearch Subdomain Panel -> kemungkinan ada waf
![](../../../../attachments/Pasted%20image%2020260422140136.png)
### e. Try another PoC to find PEAR
![](../../../../attachments/Pasted%20image%2020260422150147.png)
#### -- use first PoC combine with PEAR directory -> success get shell
![](../../../../attachments/Pasted%20image%2020260422150640.png)
#### -- reverse proxy
![](../../../../attachments/Pasted%20image%2020260422151115.png)
#### -- get user flag
![](../../../../attachments/Pasted%20image%2020260422151510.png)
# III. Privilege Escalation
## 1. Try Login as user
### a. login mysql
![](../../../../attachments/Pasted%20image%2020260422153816.png)
#### -- tables users
![](../../../../attachments/Pasted%20image%2020260422155101.png)
```
id      external_id     uuid    username        email   name_first      name_last       password        remember_token  language        root_admin      use_totp        totp_secret     totp_authenticated_atgravatar created_at      updated_at
2       NULL    5e6d956e-7be9-41ec-8016-45e434de8420    headmonitor     headmonitor@pterodactyl.htb     Head    Monitor $2y$10$3WJht3/5GOQmOXdljPbAJet2C6tHP4QoORy1PSj59qJrU0gdX5gD2    OL0dNy1nehBYdx9gQ5CT3SxDUQtDNrs02VnNesGOObatMGzKvTJAaO0B1zNU  en      1       0       NULL    NULL    1       2025-09-16 17:15:41     2025-09-16 17:15:41
3       NULL    ac7ba5c2-6fd8-4600-aeb6-f15a3906982b    phileasfogg3    phileasfogg3@pterodactyl.htb    Phileas Fogg    $2y$10$PwO0TBZA8hLB6nuSsxRqoOuXuGi3I4AVVN2IgE7mZJLzky1vGC9Pi    6XGbHcVLLV9fyVwNkqoMHDqTQ2kQlnSvKimHtUDEFvo4SjurzlqoroUgXdn8  en      0       0       NULL    NULL    1       2025-09-16 19:44:19     2025-11-07 18:28:50
```
#### -- tables user_ssh_keys
![](../../../../attachments/Pasted%20image%2020260422155807.png)

#### -- check hash -> Blowfish
![](../../../../attachments/Pasted%20image%2020260422190352.png)
#### -- failed crack using hashcat
![](../../../../attachments/Pasted%20image%2020260423091411.png)
#### -- try another hash, it work
![](../../../../attachments/Pasted%20image%2020260423093503.png)
```
!QAZ2wsx
```
#### -- it works
![](../../../../attachments/Pasted%20image%2020260423093712.png)

## 2. Recon for Root access
### a. sudo -l
![](../../../../attachments/Pasted%20image%2020260423093859.png)
### b. scan local port -> failed, it possibly opensuse linux
![](../../../../attachments/Pasted%20image%2020260423095332.png)
coba pake manual -> smtp open
![](../../../../attachments/Pasted%20image%2020260423102138.png)
### c. SUID
![](../../../../attachments/Pasted%20image%2020260423101807.png)
#### d. OS
![](../../../../attachments/Pasted%20image%2020260423103420.png)
## 3.  find CVE for opensuse Leap 15.6
https://github.com/DesertDemons/CVE-2025-6018-6019
![](../../../../attachments/Pasted%20image%2020260423134649.png)
#### -- get root shell
![](../../../../attachments/Pasted%20image%2020260423142827.png)
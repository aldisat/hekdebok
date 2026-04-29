# 0. Summary

|             | Value       |
| ----------- | ----------- |
| IP          | 10.10.11.55 |
| Credentials |             |
|             |             |
|             |             |
# 1. Active Ports
## TCP
![[../attachments/Pasted image 20250218090225.png]]
## UDP
![[../attachments/Pasted image 20250218132753.png]]
# 2. Web - titanic.htb
## frontend
![[../attachments/Pasted image 20250218091458.png]]
## Feature - Book Now
1. Book input form
![[../attachments/Pasted image 20250218091745.png]]
2. Download json
![[../attachments/Pasted image 20250218091830.png]]
3. Inject " pada setiap parameter -> diamankan respon 
![[../attachments/Pasted image 20250218092252.png]]
## Direcktory
1. feroxbuster -> nope
2. dirsearch -> nope
3. dirsearch recursive -> nope
4. dirsearch raft medium -> nope
## Subdomain
ada dev.titanic.htb ![[../attachments/Pasted image 20250218100446.png]]
# 3. Web - dev.titanic.htb
## Email
developer@titanic.htb
root@titanic.htb
![[../attachments/Pasted image 20250218144250.png]]
## Frontpage
Menggunakan gitea
![[../attachments/Pasted image 20250218120739.png]]
## Versi
Gitea version 1.22.1
![[../attachments/Pasted image 20250218130230.png]]
## Repository
- docker-config
- flash-app
![[../attachments/Pasted image 20250218130935.png]]
## Sitemap
gitea.titanic.htb
![[../attachments/Pasted image 20250218131052.png]]
## MySQL credential
![[../attachments/Pasted image 20250218135701.png]]
## Credential
Rose 
![[../attachments/Pasted image 20250218135907.png]]
Jack
![[../attachments/Pasted image 20250218135938.png]]
## Urls
![[../attachments/Pasted image 20250218140516.png]]
## Swagger
![[../attachments/Pasted image 20250218140652.png]]
## CVE
nope..
# 4. Path Traversal at Download
![[../attachments/Pasted image 20250218145950.png]]
get user.txt
![[../attachments/Pasted image 20250218151331.png]]
pada file docker pada web gitea terdapat direktori config gitea, coba buka -> berhasil
/home/developer/gitea/data/gitea/conf/app.ini
![[../attachments/Pasted image 20250218204018.png]]
download gitea db menggunakn wget dan ubah namanya ke gitea.db
![[../attachments/Pasted image 20250218204222.png]]
buka menggunakan sqlitebrowser, terdapat user password
![[../attachments/Pasted image 20250218204543.png]]
![[../attachments/Pasted image 20250220194703.png]]
![[../attachments/Pasted image 20250220194744.png]]
dapat password developer:25282528
# Priviledge Escalation
## SSH menggunakan developer
![[../attachments/Pasted image 20250220194911.png]]
## Netstat
![[../attachments/Pasted image 20250220195228.png]]
## /opt
![[../attachments/Pasted image 20250220211741.png]]
ada magick 7.1.1-35
![[../attachments/Pasted image 20250220211818.png]]
coba CVE ini bisa https://github.com/ImageMagick/ImageMagick/security/advisories/GHSA-8rxc-922v-phg8 tapi tidak bisa cat /root/root.txt
![[../attachments/Pasted image 20250220214308.png]]
coba modifikasi sudoers -> dapat root
![[../attachments/Pasted image 20250220220109.png]]
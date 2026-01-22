# 1. Check Live Host
![](Pasted%20image%2020250731095911.png)

```
192.168.10.1
192.168.10.10
192.168.10.11
192.168.10.12
192.168.10.22 
192.168.10.23
```
# 2. Port Scanning
192.168.10.10
![](Pasted%20image%2020250731132529.png)
192.168.10.11
![](Pasted%20image%2020250731132614.png)
192.168.10.12
![](Pasted%20image%2020250731143340.png)
192.168.10.22
![](Pasted%20image%2020250731143928.png)
192.168.10.23
![](Pasted%20image%2020250731150259.png)
# 3. SMB
The first thing the best do before nmap for Windows Hacking is scan SMB active host using crackmapexec
![](Pasted%20image%2020250804100322.png)
based on cme output, we know that there are 3 domain
1. sevenkingdoms.local (1 ip) -> Sid: S-1-5-21-3278613162-2407281985-2272883311
   - 192.168.10.10 - KINGSLANDING 
1. essos.local (2 ips)
    - 192.168.10.23 - BRAAVOS, singning False
   - 192.168.10.12 - MEEREEN
1. north.sevenkingdoms.local (2 ips)
   - 192.168.10.22 - CASTLEBLACK, singning False
   - 192.168.10.11 - WINTERFELL
# 4. Domain Controller
find DC IPs using nslookup and DNS IP 192.168.10.10
![](Pasted%20image%2020250806134203.png)
why uses 192.168.10.10 instead other IPS:
There are only 3 DNS server IPs
- 10.10 -> it also can be
- 10.11 -> DNS server from subdomain north.sevenkingdoms.local, so it wil not cover top domain like essos.local
- 10.12 -> it also can be

So, DC IPS of each domain are:
- sevenkingdoms.local 192.168.10.10
- north.sevenkingdoms.local 192.168.10.11
- essos.local 192.168.10.12
# 5. Set DNS 
Set up at /etc/host
```
192.168.10.10 kingslanding.sevenkingdoms.local sevenkingdoms.local kingslanding
192.168.10.11 winterfell.north.sevenkingdoms.local north.sevenkingdoms.local winterfell
192.168.10.12 meereen.essos.local essos.local meeren
192.168.10.22 castleblack.north.sevenkingdoms.local castleblack
192.168.10.23 braavos.essos.local bravoswinterfell
```
Why **`192.168.10.22` (CASTELBLACK)** doesn’t have `north.sevenkingdoms.local` and **`192.168.10.23` (BRAAVOS)** doesn’t have `essos.local` is likely because: They are **not Domain Controllers** and **not running DNS services** for those domains.
# 5. Kerberos Client
# 6. Web
```
192.168.10.10 -> 80
IIS default page

192.168.10.22 -> 80
File upload

192.168.10.23 -> 80
IIS default page


lainnya ada web tapi unusual port
```
file upload function in 192.168.10.22
![](Pasted%20image%2020250731131500.png)

# 7. Web - Upload file
![](Pasted%20image%2020250801135548.png)
upload file and check the file that u upload in `/upload` folder
![](Pasted%20image%2020250801135701.png)
upload aspx file shell, download shell from here
![](Pasted%20image%2020250801143744.png)
access the shell
![](Pasted%20image%2020250801144003.png)


**home**
convbert
spot
maregin
buy/sell
p2p
5

wallet
5

**market**
search
following
3

trade
5


**features**
4


**asset**
add fund
send
transfer
3

setting
4


binance chat\
2

scan
1

message
1

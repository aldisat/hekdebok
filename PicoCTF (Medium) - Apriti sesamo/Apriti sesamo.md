![](Pasted%20image%2020250509101753.png)
# 1. Analysis
Its a login page. The web server chalenge us to login even tho we dont have creds.
![](Pasted%20image%2020250509101850.png)

Try login random -> failed and it response with "Failed, No flag for you"
![](Pasted%20image%2020250509102043.png)

Try dirsearch, there is upload but redirect
- normal dirsearch
![](Pasted%20image%2020250509102901.png)
- a lot extention dirsearch -> same result

Lets try check backup file, our server uses php, it used to using php.bak or php~ for backup file
![](Pasted%20image%2020250509132542.png)

its works lets encode the comment
```
<?php
 if(isset($_POST[base64_decode("\144\130\x4e\154\x63\155\x35\x68\142\127\125\x3d")])&& isset($_POST[base64_decode("\143\x48\x64\x6b")])){$yuf85e0677=$_POST[base64_decode("\144\x58\x4e\154\x63\x6d\65\150\x62\127\x55\75")];$rs35c246d5=$_POST[base64_decode("\143\x48\144\153")];if($yuf85e0677==$rs35c246d5){echo base64_decode("\x50\x47\112\x79\x4c\172\x35\x47\x59\127\154\163\132\127\x51\x68\111\x45\x35\166\x49\x47\132\163\131\127\x63\x67\x5a\155\71\171\111\x48\x6c\166\x64\x51\x3d\x3d");}else{if(sha1($yuf85e0677)===sha1($rs35c246d5)){echo file_get_contents(base64_decode("\x4c\151\64\166\x5a\x6d\x78\x68\x5a\x79\65\60\145\110\x51\75"));}else{echo base64_decode("\x50\107\112\171\x4c\x7a\65\107\x59\x57\154\x73\x5a\127\x51\x68\x49\105\x35\x76\111\x47\132\x73\131\127\x63\x67\x5a\155\71\x79\x49\110\154\x76\x64\x51\x3d\75");}}}?>
```

Lil bit cheating, but i dont know what kind of encoding is that, so used chatgpt for encoding tha php script.
![](Pasted%20image%2020250509134032.png)
# 2. Exploitation
# 3. Conclussion

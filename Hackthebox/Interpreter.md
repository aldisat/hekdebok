# 1. Initial Access
## a. Port TCP
![](../../../../attachments/Pasted%20image%2020260429054855.png)
## b. Port UDP

# 2. Enumeration
## a. Web
![](../../../../attachments/Pasted%20image%2020260429043107.png)
### -- CVE
![](../../../../attachments/Pasted%20image%2020260429043255.png)
thisworks
![](../../../../attachments/Pasted%20image%2020260429051306.png)
### -- PoC
![](../../../../attachments/Pasted%20image%2020260429051553.png)
etc/passwd
![](../../../../attachments/Pasted%20image%2020260429051949.png)
db pass
![](../../../../attachments/Pasted%20image%2020260429053914.png)
keystore
![](../../../../attachments/Pasted%20image%2020260429054050.png)
### -- Connect DB
![](../../../../attachments/Pasted%20image%2020260429054652.png)
get password need to decrypt
```
u/+LBBOUnadiyFBsMOoIDPLbUR0rk59kEkPU17itdrVWA/kLMt3w+w==
```
![](../../../../attachments/Pasted%20image%2020260429054607.png)
### -- encode base64  
it hex, make it to hash
![](../../../../attachments/Pasted%20image%2020260429061948.png)
detect the hash and decrypt that
![](../../../../attachments/Pasted%20image%2020260429062801.png)
decryp it and it exhausted
![](../../../../attachments/Pasted%20image%2020260429062838.png)

### -- check hash type again, pkdf2WithHmacSHA256
![](../../../../attachments/Pasted%20image%2020260429063954.png)

# 3. Privilege Escalation

